# trains_softeng

Mermaid

erDiagram
    Types ||--o{ TRAINS : "Típusba tartozik"
    TRAINS ||--o| STAFF : "Dolgozik"
    TRAINS ||--o{ Maintenance : "Karbantartásra megy"

    Types {
        INT TrainType PK
        NVARCHAR(100) TypeName
        NVARCHAR(20) Manufacturer
        INT Capacity
        INT MaxSpeed
        INT Power
    }

    TRAINS {
        INT TrainID PK
        NVARCHAR(100) TrainName
        INT TrainType FK
        DATETIME ManufacturingDate
    }

    STAFF {
        INT StaffID PK
        INT TrainID FK
        NVARCHAR(80) StaffName
        NVARCHAR(30) Role
    }

    Maintenance {
        INT MaintenanceID PK
        INT TrainID FK
        NVARCHAR Description
        DATETIME MaintenanceDate
    }

SQL kod az adatbazoshoz

-- Types tábla létrehozása
CREATE TABLE Types (
    TrainType INT IDENTITY(1,1) PRIMARY KEY,               -- Azonosító automatikus növekvés
    TypeName NVARCHAR(100) NOT NULL,          -- Típus neve (nem lehet NULL)
    Manufacturer NVARCHAR(20) NOT NULL,       -- Gyártó (nem lehet NULL)
    Capacity INT CHECK (Capacity >= 0), -- Kapacitás legyen 0 és 1000 között
    MaxSpeed INT CHECK (MaxSpeed >= 0 ), -- Maximális sebesség legyen 0 és 500 km/h között
    Power INT CHECK (Power >= 0 AND Power <= 10000)           -- Teljesítmény legyen 0 és 10,000 kW között
);

-- TRAINS tábla létrehozása
CREATE TABLE TRAINS (
    TrainID INT IDENTITY(1,1) PRIMARY KEY,                 -- Vonat azonosító automatikus növekvés
    TrainName NVARCHAR(100) NOT NULL UNIQUE,  -- Vonat neve (nem lehet NULL és egyedi)
    TrainType INT,                            -- Vonat típusának azonosítója
    ManufacturingDate DATETIME NOT NULL,      -- Gyártás dátuma (nem lehet NULL)
    FOREIGN KEY (TrainType) REFERENCES Types(TrainType),  -- FK kapcsolódás a Types táblához
    CONSTRAINT FK_TRAINS_Types FOREIGN KEY (TrainType) REFERENCES Types(TrainType),  -- FOREIGN KEY Constraint
    CONSTRAINT CHK_ManufacturingDate CHECK (ManufacturingDate <= GETDATE()) -- A gyártás dátumának nem lehet a jövőben
);

-- STAFF tábla létrehozása
CREATE TABLE STAFF (
    StaffID INT IDENTITY(1,1) PRIMARY KEY,                 -- Dolgozó azonosító automatikus növekvés
    TrainID INT,                             -- Vonat azonosító
    StaffName NVARCHAR(100) NOT NULL,          
    Role NVARCHAR(30) NOT NULL,              
    FOREIGN KEY (TrainID) REFERENCES TRAINS(TrainID),  -- FK kapcsolódás a TRAINS táblához
    CONSTRAINT FK_STAFF_TRAINS FOREIGN KEY (TrainID) REFERENCES TRAINS(TrainID),  -- FOREIGN KEY Constraint

);

-- Maintenance tábla létrehozása
CREATE TABLE Maintenance (
    MaintenanceID INT IDENTITY(1,1) PRIMARY KEY,        
    TrainID INT,                             
    Description NVARCHAR(255) NOT NULL,      
    MaintenanceDate DATETIME NOT NULL,       
    FOREIGN KEY (TrainID) REFERENCES TRAINS(TrainID),  -- FK kapcsolódás a TRAINS táblához
    CONSTRAINT FK_Maintenance_TRAINS FOREIGN KEY (TrainID) REFERENCES TRAINS(TrainID),  -- FOREIGN KEY Constraint
);
