# Running a PostgreSQL Procedure in a Cron Job

## Overview

This guide will help you create a cron job to automatically run a PostgreSQL procedure. You'll learn how to download a script from a repository using `wget`, make the script executable, and schedule it with cron.

## Step 1: Download the Script

### Purpose
To obtain the script file from a repository, use `wget`. This step ensures you have the latest version of the script.

### Instructions

1. **Download the Script**:
   Use `wget` to download the script from a repository. Replace `<URL>` with the actual URL of the script in your repository:

   ```bash
   wget <URL> -O call_procedure.sh
   ```

   - **Explanation**:
     - `wget <URL>`: Downloads the file from the specified URL.
     - `-O call_procedure.sh`: Saves the file with the name `call_procedure.sh`.

2. **Verify the Download**:
   Check that the file has been downloaded and contains the correct content:

   ```bash
   cat call_procedure.sh
   ```

## Step 2: Make the Script Executable

### Purpose
To run the script, it must be executable.

### Instructions

Run the following command to make the script executable:

```bash
chmod +x call_procedure.sh
```

- **Explanation**:
  - `chmod +x`: Grants execute permissions to the script.

## Step 3: Schedule the Script with Cron

### Purpose
Use cron to schedule the script to run automatically at specified times.

### Instructions

1. **Open Crontab for Editing**:
   Open the crontab configuration file using `nano`:

   ```bash
   crontab -e
   ```

   - **Explanation**:
     - `crontab -e`: Opens the crontab file for editing.

2. **Add the Cron Job**:
   Add the following line to schedule the script to run daily at 2 AM. Adjust the path if necessary:

   ```bash
   0 2 * * * /path/to/call_procedure.sh
   ```

   - **Explanation**:
     - `0 2 * * *`: Runs the script every day at 2 AM.
     - `/path/to/call_procedure.sh`: Full path to your script.

3. **Save and Exit Crontab**:
   - Press `Ctrl + O` to save the file.
   - Press `Enter` to confirm.
   - Press `Ctrl + X` to exit.

   - **Explanation**:
     - `Ctrl + O`: Saves the changes.
     - `Ctrl + X`: Exits the editor.

## Sample Shell Script (`call_procedure.sh`)

Here is a sample shell script that connects to a PostgreSQL database and executes a stored procedure:

```bash
#!/bin/bash

# Set your database connection parameters
DB_HOST=103.23.198.61
DB_PORT=5432
DB_NAME=your_database_name
DB_USER=your_database_user
DB_PASSWORD=your_database_password

# Set the name of the procedure you want to call
PROCEDURE_NAME=get_user_data

# Set the log file for tracking execution details in the home directory
LOG_FILE=~/.procedure_logs/$(date +'%Y-%m-%d')_get_user_data.log

# Create the log directory if it doesn't exist
mkdir -p ~/.procedure_logs

# Log the start of the execution
echo "$(date) - Starting execution of procedure $PROCEDURE_NAME" >> $LOG_FILE

# Call the procedure using psql with parameters (if any)
psql -h $DB_HOST -p $DB_PORT -d $DB_NAME -U $DB_USER -c "CALL get_user_data()" >> $LOG_FILE 2>&1

# Log the end of the execution with success or failure status
if [ $? -eq 0 ]; then
    echo "$(date) - Execution of procedure $PROCEDURE_NAME successful." >> $LOG_FILE
else
    echo "$(date) - Execution of procedure $PROCEDURE_NAME failed." >> $LOG_FILE
fi
```

## Example Crontab Entry

Here’s an example of a crontab entry:

```cron
0 2 * * * /home/user/call_procedure.sh
```

- This will run your script every day at 2 AM and log the results.

## Troubleshooting Tips

- **Permissions**:
  Ensure the script is executable with `chmod +x call_procedure.sh`.

- **Paths**:
  Verify that the paths to the script and log files are correct.

- **Logs**:
  Check the log files located in `~/.procedure_logs` for any errors or success messages.
