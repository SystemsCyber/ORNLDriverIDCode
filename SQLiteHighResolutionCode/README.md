# SQLite High-Resolution Data Processing

## Overview
This folder contains scripts to process raw driving data into a high-resolution SQLite database format. The scripts take in various raw data sources, process them, and store the information in a structured SQLite database, making it easier for analysis and further data processing.

## Scripts Overview
### 1. **SparkfunGPSToSQLite.py**
   - Converts raw GPS data into an SQLite database.
   - Ensures timestamps align with other data sources for synchronization.
   - Saves the data in SparkfunGPSData table

### 2. **CANBinFileToCANDumpFormat.py**
   - Converts raw CAN binary log files into a readable format.
   - Parses and structures the CAN data for further processing.
   - This only needs to be used if the truckcape log file was not usable.

### 3. **CANDataSPNDecoder2023.py**
   - Decodes CAN data using the **J1939 digital annex**.
   - Extracts meaningful parameters (SPNs) from the raw CAN data.

### 4. **CANDataToSQLite.py**
   - Processes and stores CAN data into an SQLite database.
   - Uses decoded data from `CANDataSPNDecoder2023.py`.
   - Saves the data in SPNData table.

### 5. **HeartRateToSQLite.py**
   - Converts raw heart rate data from the **Empatica E4 wristband** into an SQLite format.
   - Synchronizes heart rate timestamps with other datasets.
   - Saves the data in HeartData table.

### 6. **VBOXToSQLite.py**
   - Processes VBOX data (GPS and inertial data) into an SQLite database.
   - Ensures alignment with CAN data timestamps.
   - Saves the data in VBOXData table.

### 7. **FixVBOXTimestamps.py**
   - Adjusts the VBOX timestamps to match CAN data timestamps.
   - Steps:
     1. Copies the first CAN timestamp to VBOX data.
     2. Iterates through possible offsets (-100s to +100s, checking every 0.01s).
     3. Finds the offset with the highest correlation between VBOX speed and CAN speed.
     4. Adjusts all VBOX timestamps accordingly.
   - Most subjects achieved a **0.999 correlation** between VBOX and CAN speed data.
   - Subjects with **missing or low-quality VBOX data**: **G1S15, G1S16, G2S13, G3S13**.
   - Speed Comparison Before Shift (Not Fully Aligned)
      - ![Before Timestamp Correction](InitialSpeedComparison.png)
   - Speed Comparison After Shift (Both Speed Plots Fully Overlap)
      - ![After Timestamp Correction](SpeedComparisonAfterShift.png)

## Usage Instructions
1. Ensure all raw data files are available.
2. Edit the python files with the correct directories where the files are stored.
3. Run each script in sequence to generate the SQLite database:
   ```bash
   python3 CANBinFileToCANDumpFormat.py <Path to candump file>                             # Convert CAN binary to readable format
   python3 CANDataSPNDecoder2023.py <Path to candump file> <Path to J1939 json file>       # Decodes CAN data
   python3 SparkfunGPSToSQLite.py <Path to candump file>                                   # Store Sparkfun GPS Data
   python3 CANDataToSQLite.py <Path to candump file> <Path to J1939 json file>             # Store CAN data in SQLite
   python3 HeartRateToSQLite.py <Path to EDA file> <Path to HR file> <Path to IBI file>    # Store heart rate data in SQLite
   python3 VBOXToSQLite.py <Path to VBOX file>                                             # Store VBOX data in SQLite
   python3 FixVBOXTimestamps.py                                                            # Adjust VBOX timestamps
   ```
4. The final output after all files have been executed for all 50 participants will be 50 **high-resolution SQLite databases** containing driving data. These will be in 4 tables, SparkfunGPSData, SPNData, HeartData, and VBOXData.

## VBOX GPS Speed to CANbus Wheel-Based Vehicle Speed Correlations for all subjects:
# Correlation Analysis for Driver ID Data

This summarizes the correlation results for timestamp adjustments in the Driver ID datasets. The correlations were calculated using the `FixVBOXTimestamps.py` script, with the maximum correlation and the corresponding shift (in seconds) for the VBOX GPS Speed to match the CANbus Wheel-Based Vehicle Speed.

## Group 1
- **S01:** Correlation: 0.99987, Shift: 10.21 s
- **S02:** Correlation: 0.99983, Shift: 3.34 s
- **S03:** Correlation: 0.99987, Shift: 2.44 s
- **S04:** Correlation: 0.83595, Shift: -23.68 s (Lower Correlation Due to Sampling rate of the CANbus data being significantly lower than other drives)
- **S05:** Correlation: 0.99989, Shift: 8.69 s
- **S06:** Correlation: 0.99984, Shift: -0.37 s
- **S07:** Correlation: 0.99975, Shift: -0.13 s
- **S08:** Correlation: 0.99987, Shift: 42.76 s
- **S09:** Correlation: 0.74942, Shift: -10.58 s (Lower Correlation due to very small portion of VBOX Data Cut towards the end)
- **S10:** Correlation: 0.99965, Shift: -3.16 s
- **S11:** Correlation: 0.99972, Shift: -4.85 s
- **S12:** Correlation: 0.99959, Shift: -2.19 s
- **S13:** Correlation: 0.99984, Shift: -5.55 s
- **S14:** Correlation: 0.99964, Shift: 1.85 s
- **S15:** *NO USABLE VBOX Data*
- **S16:** *NO USABLE VBOX Data*
- **S17:** Correlation: 0.99979, Shift: -7.62 s

## Group 2
- **S01:** Correlation: 0.99987, Shift: 5.58 s
- **S02:** Correlation: 0.99980, Shift: 2.07 s
- **S03:** Correlation: 0.99986, Shift: 3.19 s
- **S04:** Correlation: 0.99989, Shift: 7.07 s
- **S05:** Correlation: 0.99978, Shift: 4.57 s
- **S06:** Correlation: 0.99989, Shift: 3.14 s
- **S07:** Correlation: 0.99992, Shift: 5.32 s
- **S08:** Correlation: 0.99985, Shift: 3.58 s
- **S09:** Correlation: 0.99976, Shift: 2.35 s
- **S10:** Correlation: 0.99988, Shift: 2.38 s
- **S11:** Correlation: 0.99977, Shift: 1.19 s
- **S12:** Correlation: 0.99982, Shift: 2.08 s
- **S13:** *NO USABLE VBOX Data*
- **S14:** Correlation: 0.99972, Shift: -1.89 s
- **S15:** Correlation: 0.99982, Shift: -5.48 s
- **S16:** Correlation: 0.99984, Shift: 3.48 s

## Group 3
- **S01:** Correlation: 0.99983, Shift: 1.89 s
- **S02:** Correlation: 0.99990, Shift: 3.46 s
- **S03:** Correlation: 0.99976, Shift: 2.43 s
- **S04:** Correlation: 0.99985, Shift: -22.07 s
- **S05:** Correlation: 0.99986, Shift: 2.40 s
- **S06:** Correlation: 0.99980, Shift: 3.22 s
- **S07:** Correlation: 0.99981, Shift: 2.92 s
- **S08:** Correlation: 0.99989, Shift: 3.41 s
- **S09**: Correlation: 0.9998,  Shift: 2.81s
- **S10**: Correlation: 0.9997,  Shift: -8.21s
- **S11**: Correlation: 0.9998,  Shift: 4.21s
- **S12**: Correlation: 0.9998,  Shift: 2.69s
- **S13**: *NO USABLE VBOX Data*
- **S14**: Correlation: 0.9978,  Shift: 3.73s
- **S15**: Correlation: 0.9998,  Shift: 2.44s
- **S16**: Correlation: 0.9998,  Shift: -24.30s
- **S17**: Correlation: 0.9999,  Shift: -6.93s

Timestamps were successfully updated for all files.



