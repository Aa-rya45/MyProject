# EXPERIMENT: APACHE AIRFLOW — WEDNESDAY DATE AND TIME DAG

**Aim:** Install Apache Airflow Server and create a DAG to display the date and time every Wednesday at 9:30 AM.

**Software:** Windows, WSL Ubuntu, Python 3.12.9, Apache Airflow 3.1.1

## PART 1: INSTALL UBUNTU

```powershell
# STEP 1: Open Windows PowerShell as Administrator

# Check whether WSL is installed
wsl --status

# Check available Ubuntu distributions
wsl --list --verbose

# If Ubuntu is NOT installed, run this command
wsl --install -d Ubuntu

# Restart the computer if requested
# Open Ubuntu and create a username and password

# If Ubuntu is already installed, open it using
wsl
```

## PART 2: INSTALL PYTHON IN UBUNTU

```bash
# STEP 2: Update Ubuntu packages
sudo apt update

# Upgrade packages
sudo apt upgrade -y

# STEP 3: Install required dependencies
sudo apt install -y build-essential curl git wget make libssl-dev zlib1g-dev libbz2-dev libreadline-dev libsqlite3-dev libffi-dev liblzma-dev tk-dev xz-utils ca-certificates

# STEP 4: Check whether pyenv is installed
pyenv --version

# If pyenv is NOT installed, install it
curl -fsSL https://pyenv.run | bash

# Configure pyenv
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc
echo '[[ -d $PYENV_ROOT/bin ]] && export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc
echo 'eval "$(pyenv init - bash)"' >> ~/.bashrc

# Reload terminal configuration
source ~/.bashrc

# Verify pyenv installation
pyenv --version

# STEP 5: Install Python 3.12.9
pyenv install 3.12.9

# STEP 6: Create Airflow project folder
mkdir -p ~/airflow_project

# Open project folder
cd ~/airflow_project

# Select Python 3.12.9
pyenv local 3.12.9

# Check Python version
python --version

# Expected Output: Python 3.12.9
```

## PART 3: INSTALL APACHE AIRFLOW

```bash
# STEP 7: Create virtual environment
python -m venv airflow312_venv

# STEP 8: Activate virtual environment
source airflow312_venv/bin/activate

# STEP 9: Upgrade pip
python -m pip install --upgrade pip

# STEP 10: Install Apache Airflow 3.1.1
python -m pip install --timeout 120 --retries 10 "apache-airflow==3.1.1" --constraint "https://raw.githubusercontent.com/apache/airflow/constraints-3.1.1/constraints-3.12.txt"

# STEP 11: Verify Airflow installation
airflow version

# Expected Output: 3.1.1
```

## PART 4: START AIRFLOW SERVER

```bash
# STEP 12: Set Airflow home directory
export AIRFLOW_HOME=~/airflow

# STEP 13: Start Apache Airflow server
airflow standalone

# IMPORTANT: Keep this Ubuntu terminal running
# Do not close Terminal 1
```

### STEP 14: GO TO CHROME

```text
# Open Google Chrome

# Enter this URL
http://localhost:8080

# Airflow login page will open
```

### STEP 15: GET LOGIN PASSWORD

```bash
# Open SECOND Ubuntu terminal
# Keep Terminal 1 running

# Open project folder
cd ~/airflow_project

# Activate virtual environment
source airflow312_venv/bin/activate

# Set Airflow home
export AIRFLOW_HOME=~/airflow

# Display generated login credentials
cat ~/airflow/simple_auth_manager_passwords.json.generated

# Copy the generated password privately
# Return to Chrome and log in
# Do NOT upload the password to GitHub
```

## PART 5: CREATE DAG FILE

```bash
# STEP 16: Continue in SECOND Ubuntu terminal

# Create DAG directory
mkdir -p ~/airflow/dags

# STEP 17: Create Python DAG file
nano ~/airflow/dags/wednesday_datetime.py

# Nano editor will open
# Paste the Python code given below
```

### STEP 18: PYTHON DAG CODE

Save this code in `wednesday_datetime.py`.

```python
# STEP 1: Import required libraries
from airflow.sdk import DAG, task
from datetime import datetime
from zoneinfo import ZoneInfo

# STEP 2: Create DAG
with DAG(
    dag_id="wednesday_datetime",

    # Set start date and Indian timezone
    start_date=datetime(
        2026, 10, 21,
        tzinfo=ZoneInfo("Asia/Kolkata")
    ),

    # STEP 3: Schedule DAG for Wednesday at 9:30 AM
    # 30 = minute
    # 9 = hour
    # * = every date
    # * = every month
    # 3 = Wednesday
    schedule="30 9 * * 3",

    # Do not execute missed past runs
    catchup=False,

    tags=["practical"],

) as dag:

    # STEP 4: Create task
    @task
    def display_datetime():

        # Get current date and time in IST
        now = datetime.now(ZoneInfo("Asia/Kolkata"))

        # Display date
        print("Date:", now.strftime("%d-%m-%Y"))

        # Display time
        print("Time:", now.strftime("%I:%M:%S %p"))

        # Display day
        print("Day:", now.strftime("%A"))

    # STEP 5: Add task to DAG
    display_datetime()
```

### STEP 19: SAVE DAG FILE

```text
# Press Ctrl + O
# Press Enter
# Press Ctrl + X

# File saved successfully
# Return to Ubuntu terminal
```

## PART 6: VERIFY DAG

```bash
# STEP 20: Display all available DAGs
airflow dags list

# Expected: wednesday_datetime should appear

# STEP 21: Check DAG errors if it does not appear
airflow dags list-import-errors

# STEP 22: Check DAG file location
ls -l ~/airflow/dags/
```

## PART 7: RUN DAG USING AIRFLOW WEBSITE

```text
# STEP 23: GO TO GOOGLE CHROME

# Open
http://localhost:8080

# STEP 24: Click "Dags" in the left sidebar

# STEP 25: Search for
wednesday_datetime

# STEP 26: Click the DAG name
wednesday_datetime

# STEP 27: Enable the DAG using the toggle
# Turn ON if paused

# STEP 28: Click "Trigger"
# This manually runs the DAG for testing

# STEP 29: Wait for the DAG run to finish

# STEP 30: Click "Runs"

# STEP 31: Click the latest DAG Run ID

# STEP 32: Click the task
display_datetime

# STEP 33: Click "Logs"

# STEP 34: Check date, time and day in the output
```

## PART 8: EXPECTED OUTPUT

Example output for a Wednesday scheduled run:

```text
Date: 28-10-2026
Time: 09:30:00 AM
Day: Wednesday
```

**Note:** If you manually trigger the DAG during the practical exam, it will show the actual date and time when it runs. The automatic schedule remains every Wednesday at 9:30 AM IST. Airflow's scheduler must be running for scheduled execution.

## PART 9: IMPORTANT COMMANDS

```bash
# Check Python version
python --version

# Check Airflow version
airflow version

# Check pyenv versions
pyenv versions

# Check available storage
df -h /

# Check DAG list
airflow dags list

# Check DAG import errors
airflow dags list-import-errors

# Check DAG files
ls -l ~/airflow/dags/

# Open existing DAG file
nano ~/airflow/dags/wednesday_datetime.py

# Start Airflow server (when not already running)
airflow standalone
```

## PART 10: RESULT

Apache Airflow Server was successfully installed and configured without Docker. A DAG named `wednesday_datetime` was created to display the current date, time and day every Wednesday at 9:30 AM IST. The DAG was manually tested and the output was verified using the Airflow task logs.
