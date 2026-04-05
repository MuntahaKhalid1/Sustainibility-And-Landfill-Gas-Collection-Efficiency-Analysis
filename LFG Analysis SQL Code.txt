-- ============================================
-- Query 1: State Level LFG Collection Efficiency Analysis
-- ============================================

SELECT 
    State,
    COUNT(DISTINCT "Landfill ID") AS total_landfills,
    SUM("Waste in Place (tons)") AS total_waste_in_place_tons,
    ROUND(SUM("LFG Collected (mmscfd)"), 2) AS total_lfg_collected,
    ROUND(SUM(CAST("LFG Flared (mmscfd)" AS REAL)), 2) AS total_lfg_flared,
    CASE 
        WHEN (SUM("LFG Collected (mmscfd)") + 
            SUM(CAST("LFG Flared (mmscfd)" AS REAL))) > 0
        THEN ROUND(
            SUM("LFG Collected (mmscfd)") * 100.0 / 
            (SUM("LFG Collected (mmscfd)") + 
            SUM(CAST("LFG Flared (mmscfd)" AS REAL))), 1)
        ELSE NULL
    END AS collection_efficiency_pct
FROM Sustainibility_Data
WHERE State IS NOT NULL
GROUP BY State
ORDER BY total_waste_in_place_tons DESC;


-- ============================================
-- Query 2: LFG Energy Project Growth Trends Over Time
-- ============================================

SELECT 
    SUBSTR("Project Start Date", -4, 4) AS project_year,
    "Project Type Category",
    "LFG Energy Project Type",
    COUNT("Project ID") AS projects_started,
    ROUND(SUM(CAST("Actual MW Generation" AS REAL)), 2) AS total_mw_generated
FROM Sustainibility_Data
WHERE "Project Start Date" IS NOT NULL
AND "Project Start Date" != ''
AND "Project Type Category" IS NOT NULL
GROUP BY project_year, "Project Type Category", "LFG Energy Project Type"
ORDER BY project_year ASC;


-- ============================================
-- Query 3: Operational Project Efficiency Gap by State
-- ============================================

SELECT 
    State,
    COUNT("Project ID") AS total_projects,
    ROUND(SUM(CAST("Rated MW Capacity" AS REAL)), 2) AS total_rated_mw,
    ROUND(SUM(CAST("Actual MW Generation" AS REAL)), 2) AS total_actual_mw,
    ROUND(SUM("Current Year Emission Reductions (MMTCO2e/yr) - Direct"), 4) 
        AS total_emission_reductions,
    CASE 
        WHEN SUM(CAST("Rated MW Capacity" AS REAL)) > 0 
        THEN ROUND(
            SUM(CAST("Actual MW Generation" AS REAL)) / 
            SUM(CAST("Rated MW Capacity" AS REAL)) * 100, 1)
        ELSE 0 
    END AS efficiency_percentage
FROM Sustainibility_Data
WHERE "Current Project Status" = 'Operational'
AND State IS NOT NULL
GROUP BY State
ORDER BY efficiency_percentage ASC;


-- ============================================
-- Master Dataset: Combined State Level Analysis for Tableau
-- ============================================

CREATE TABLE LFG_Master AS
SELECT 
    s.State,
    COUNT(DISTINCT s."Landfill ID") AS total_landfills,
    SUM(s."Waste in Place (tons)") AS total_waste_in_place_tons,
    ROUND(SUM(s."LFG Collected (mmscfd)"), 2) AS total_lfg_collected,
    ROUND(SUM(CAST(s."LFG Flared (mmscfd)" AS REAL)), 2) AS total_lfg_flared,
    CASE 
        WHEN (SUM(s."LFG Collected (mmscfd)") + 
            SUM(CAST(s."LFG Flared (mmscfd)" AS REAL))) > 0
        THEN ROUND(
            SUM(s."LFG Collected (mmscfd)") * 100.0 / 
            (SUM(s."LFG Collected (mmscfd)") + 
            SUM(CAST(s."LFG Flared (mmscfd)" AS REAL))), 1)
        ELSE NULL
    END AS collection_efficiency_pct,
    COUNT(s."Project ID") AS total_projects,
    ROUND(SUM(CAST(s."Rated MW Capacity" AS REAL)), 2) AS total_rated_mw,
    ROUND(SUM(CAST(s."Actual MW Generation" AS REAL)), 2) AS total_actual_mw,
    ROUND(SUM(s."Current Year Emission Reductions (MMTCO2e/yr) - Direct"), 4) 
        AS total_emission_reductions_mmtco2e,
    CASE 
        WHEN SUM(CAST(s."Rated MW Capacity" AS REAL)) > 0 
        THEN ROUND(
            SUM(CAST(s."Actual MW Generation" AS REAL)) / 
            SUM(CAST(s."Rated MW Capacity" AS REAL)) * 100, 1)
        ELSE 0 
    END AS mw_efficiency_percentage
FROM Sustainibility_Data s
WHERE s.State IS NOT NULL
GROUP BY s.State
ORDER BY total_waste_in_place_tons DESC;