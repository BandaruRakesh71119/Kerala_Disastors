# Kerala_Disastors

-- Kerala Disasters Database Management System
-- Complete SQL Code

-- Create Database
CREATE DATABASE IF NOT EXISTS kerala_disasters;
USE kerala_disasters;

-- 1. Disaster Types Reference Table
CREATE TABLE disaster_types (
    type_id INT PRIMARY KEY AUTO_INCREMENT,
    type_name VARCHAR(50) UNIQUE NOT NULL,
    description TEXT
);

-- 2. Districts Reference Table
CREATE TABLE districts (
    district_id INT PRIMARY KEY AUTO_INCREMENT,
    district_name VARCHAR(50) UNIQUE NOT NULL,
    area_sqkm DECIMAL(10,2),
    population INT
);

-- 3. Main Disasters Table
CREATE TABLE disasters (
    disaster_id INT PRIMARY KEY AUTO_INCREMENT,
    disaster_type_id INT,
    disaster_name VARCHAR(100) NOT NULL,
    start_date DATE NOT NULL,
    end_date DATE,
    casualties INT DEFAULT 0,
    injured INT DEFAULT 0,
    displaced_people INT DEFAULT 0,
    estimated_damage_crores DECIMAL(12,2),
    description TEXT,
    severity_level ENUM('Low', 'Medium', 'High', 'Severe') DEFAULT 'Medium',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (disaster_type_id) REFERENCES disaster_types(type_id)
);

-- 4. Junction Table for Disasters and Affected Districts
CREATE TABLE disaster_districts (
    id INT PRIMARY KEY AUTO_INCREMENT,
    disaster_id INT,
    district_id INT,
    impact_level ENUM('Low', 'Medium', 'High') DEFAULT 'Medium',
    district_casualties INT DEFAULT 0,
    district_damage_crores DECIMAL(12,2),
    FOREIGN KEY (disaster_id) REFERENCES disasters(disaster_id) ON DELETE CASCADE,
    FOREIGN KEY (district_id) REFERENCES districts(district_id) ON DELETE CASCADE
);

-- 5. Relief Measures Table
CREATE TABLE relief_measures (
    relief_id INT PRIMARY KEY AUTO_INCREMENT,
    disaster_id INT,
    measure_type VARCHAR(50),
    description TEXT,
    funds_allocated DECIMAL(12,2),
    implementing_agency VARCHAR(100),
    start_date DATE,
    end_date DATE,
    status ENUM('Planned', 'Ongoing', 'Completed') DEFAULT 'Planned',
    FOREIGN KEY (disaster_id) REFERENCES disasters(disaster_id) ON DELETE CASCADE
);

-- 6. Weather Data Table (for correlation analysis)
CREATE TABLE weather_data (
    weather_id INT PRIMARY KEY AUTO_INCREMENT,
    district_id INT,
    record_date DATE,
    rainfall_mm DECIMAL(8,2),
    temperature_max DECIMAL(4,1),
    temperature_min DECIMAL(4,1),
    humidity_percent INT,
    FOREIGN KEY (district_id) REFERENCES districts(district_id) ON DELETE CASCADE
);

-- Insert Master Data

-- Insert Disaster Types
INSERT INTO disaster_types (type_name, description) VALUES
('Flood', 'Heavy rainfall causing river overflow and flooding'),
('Landslide', 'Soil and rock movement in hilly areas'),
('Cyclone', 'Tropical storms with high winds and heavy rain'),
('Drought', 'Extended periods of water scarcity'),
('Earthquake', 'Seismic activity causing ground shaking'),
('Epidemic', 'Widespread outbreak of infectious diseases'),
('Lightning', 'Electrical discharges during thunderstorms'),
('Coastal Erosion', 'Loss of coastal land due to wave action');

-- Insert Kerala Districts
INSERT INTO districts (district_name, area_sqkm, population) VALUES
('Thiruvananthapuram', 2192, 3301427),
('Kollam', 2491, 2629703),
('Pathanamthitta', 2637, 119553),
('Alappuzha', 1414, 2121943),
('Kottayam', 2203, 1974551),
('Idukki', 4358, 1108974),
('Ernakulam', 3063, 3279860),
('Thrissur', 3032, 3110327),
('Palakkad', 4480, 2809704),
('Malappuram', 3550, 4110956),
('Kozhikode', 2344, 3086293),
('Wayanad', 2131, 816558),
('Kannur', 2966, 2523003),
('Kasaragod', 1992, 1302600);

-- Insert Sample Disaster Data
INSERT INTO disasters (disaster_type_id, disaster_name, start_date, end_date, casualties, injured, displaced_people, estimated_damage_crores, severity_level, description) VALUES
(1, '2018 Kerala Floods', '2018-08-01', '2018-08-20', 483, 140, 1047000, 31000.00, 'Severe', 'Worst flooding in nearly a century due to unusually high rainfall'),
(1, '2019 Kerala Floods', '2019-08-08', '2019-08-17', 121, 40, 287000, 4200.00, 'High', 'Heavy monsoon rains causing flash floods'),
(2, '2020 Idukki Landslides', '2020-08-06', '2020-08-10', 66, 15, 2000, 150.00, 'High', 'Series of landslides in Rajamala region'),
(3, 'Cyclone Ockhi 2017', '2017-11-29', '2017-12-06', 75, 150, 20000, 500.00, 'High', 'Severe cyclonic storm affecting coastal areas'),
(1, '2021 Kerala Floods', '2021-10-16', '2021-10-20', 42, 25, 15000, 800.00, 'Medium', 'Localized flooding in central Kerala');

-- Insert Disaster-District Relationships
INSERT INTO disaster_districts (disaster_id, district_id, impact_level, district_casualties, district_damage_crores) VALUES
(1, 6, 'High', 80, 5000.00),  -- Idukki in 2018 floods
(1, 7, 'High', 45, 4500.00),  -- Ernakulam
(1, 8, 'Medium', 35, 4000.00), -- Thrissur
(2, 6, 'High', 25, 800.00),   -- Idukki in 2019 floods
(2, 7, 'Medium', 18, 700.00),  -- Ernakulam
(3, 6, 'Severe', 66, 150.00),  -- Idukki landslides
(4, 1, 'High', 25, 200.00),    -- Thiruvananthapuram cyclone
(4, 2, 'Medium', 15, 150.00),  -- Kollam
(5, 7, 'Medium', 8, 300.00),   -- Ernakulam 2021 floods
(5, 8, 'Low', 5, 200.00);      -- Thrissur

-- Insert Relief Measures
INSERT INTO relief_measures (disaster_id, measure_type, description, funds_allocated, implementing_agency, start_date, end_date, status) VALUES
(1, 'Financial Aid', 'Immediate relief funds for affected families', 2000.00, 'State Disaster Management', '2018-08-15', '2018-12-31', 'Completed'),
(1, 'Reconstruction', 'Rebuilding damaged infrastructure', 5000.00, 'PWD Kerala', '2018-09-01', '2019-12-31', 'Completed'),
(2, 'Compensation', 'Compensation for loss of life and property', 800.00, 'Revenue Department', '2019-08-20', '2020-03-31', 'Completed'),
(3, 'Rescue Operation', 'NDRF and Army rescue missions', 50.00, 'NDRF', '2020-08-07', '2020-08-12', 'Completed'),
(4, 'Fisherman Rescue', 'Coastal rescue operations for fishermen', 100.00, 'Indian Coast Guard', '2017-12-01', '2017-12-10', 'Completed');

-- Insert Weather Data
INSERT INTO weather_data (district_id, record_date, rainfall_mm, temperature_max, temperature_min, humidity_percent) VALUES
(6, '2018-08-15', 350.5, 28.5, 23.2, 95),  -- Idukki during 2018 floods
(7, '2018-08-15', 280.2, 29.1, 24.0, 92),  -- Ernakulam
(6, '2019-08-10', 180.7, 29.5, 23.8, 88),  -- Idukki 2019
(7, '2019-08-10', 150.3, 30.2, 24.5, 85),  -- Ernakulam
(6, '2020-08-08', 420.8, 27.8, 22.5, 96);  -- Idukki landslides

-- Create Useful Views

-- View for Disaster Summary
CREATE VIEW disaster_summary AS
SELECT 
    d.disaster_id,
    d.disaster_name,
    dt.type_name as disaster_type,
    d.start_date,
    d.end_date,
    d.casualties,
    d.injured,
    d.displaced_people,
    d.estimated_damage_crores,
    d.severity_level
FROM disasters d
JOIN disaster_types dt ON d.disaster_type_id = dt.type_id;

-- View for District-wise Impact
CREATE VIEW district_impact AS
SELECT 
    dist.district_name,
    d.disaster_name,
    dd.impact_level,
    dd.district_casualties,
    dd.district_damage_crores,
    d.start_date
FROM disaster_districts dd
JOIN districts dist ON dd.district_id = dist.district_id
JOIN disasters d ON dd.disaster_id = d.disaster_id;

-- View for Year-wise Analysis
CREATE VIEW yearly_disaster_analysis AS
SELECT 
    YEAR(start_date) as year,
    COUNT(*) as total_disasters,
    SUM(casualties) as total_casualties,
    SUM(injured) as total_injured,
    SUM(displaced_people) as total_displaced,
    SUM(estimated_damage_crores) as total_damage_crores
FROM disasters
GROUP BY YEAR(start_date);

-- Sample Queries for Analysis

-- Query 1: Get all disasters with details
SELECT * FROM disaster_summary ORDER BY start_date DESC;

-- Query 2: Most affected districts
SELECT 
    district_name,
    COUNT(*) as times_affected,
    SUM(district_casualties) as total_casualties
FROM district_impact 
GROUP BY district_name 
ORDER BY total_casualties DESC;

-- Query 3: Disaster types frequency
SELECT 
    dt.type_name,
    COUNT(d.disaster_id) as occurrence_count
FROM disaster_types dt
LEFT JOIN disasters d ON dt.type_id = d.disaster_type_id
GROUP BY dt.type_name
ORDER BY occurrence_count DESC;

-- Query 4: Relief measures by disaster
SELECT 
    d.disaster_name,
    rm.measure_type,
    rm.funds_allocated,
    rm.implementing_agency,
    rm.status
FROM relief_measures rm
JOIN disasters d ON rm.disaster_id = d.disaster_id
ORDER BY d.start_date DESC;

-- Query 5: Monthly disaster analysis
SELECT 
    YEAR(start_date) as year,
    MONTH(start_date) as month,
    COUNT(*) as disaster_count
FROM disasters
GROUP BY YEAR(start_date), MONTH(start_date)
ORDER BY year DESC, month DESC;

-- Create Indexes for Performance
CREATE INDEX idx_disasters_date ON disasters(start_date);
CREATE INDEX idx_disasters_type ON disasters(disaster_type_id);
CREATE INDEX idx_disasters_severity ON disasters(severity_level);
CREATE INDEX idx_district_impact ON disaster_districts(district_id, impact_level);
CREATE INDEX idx_weather_date ON weather_data(record_date);

-- Stored Procedure for Adding New Disaster
DELIMITER //
CREATE PROCEDURE AddNewDisaster(
    IN p_type_name VARCHAR(50),
    IN p_disaster_name VARCHAR(100),
    IN p_start_date DATE,
    IN p_end_date DATE,
    IN p_casualties INT,
    IN p_damage DECIMAL(12,2),
    IN p_severity ENUM('Low', 'Medium', 'High', 'Severe')
)
BEGIN
    DECLARE type_id INT;
    
    -- Get or create disaster type
    SELECT t.type_id INTO type_id 
    FROM disaster_types t 
    WHERE t.type_name = p_type_name;
    
    IF type_id IS NULL THEN
        INSERT INTO disaster_types (type_name) VALUES (p_type_name);
        SET type_id = LAST_INSERT_ID();
    END IF;
    
    -- Insert new disaster
    INSERT INTO disasters (disaster_type_id, disaster_name, start_date, end_date, casualties, estimated_damage_crores, severity_level)
    VALUES (type_id, p_disaster_name, p_start_date, p_end_date, p_casualties, p_damage, p_severity);
    
    SELECT LAST_INSERT_ID() as new_disaster_id;
END //
DELIMITER ;

-- Example usage of stored procedure
-- CALL AddNewDisaster('Flood', '2023 Kerala Floods', '2023-08-01', '2023-08-05', 15, 300.00, 'Medium');

COMMIT;
