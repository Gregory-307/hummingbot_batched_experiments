# Hummingbot Deploy with Custom Experimentation Framework

This repository is a fork of the standard `hummingbot/deploy` project, enhanced with a custom framework for running and analyzing backtesting experiments in batches.

## Overview

This system provides two powerful, parallel workflows:

1.  **Hummingbot Deploy Core:** The standard system for deploying and managing live Hummingbot instances via a graphical web UI.
2.  **Custom Experimentation Framework:** A suite of local Python scripts designed for rapid, code-based development. It allows you to define, run, and analyze batches of backtests against your own custom strategies without ever touching the UI. It includes a dedicated Streamlit dashboard for visualizing experiment results.

The primary goal of this fork is to accelerate the strategy ideation and evaluation cycle.

---

## Setup Instructions

Please follow the instructions for your operating system.

### **Windows Setup (Tested)**

#### **Step 1: Install Prerequisites**
Before you begin, ensure you have the following installed and running on your Windows machine:

-   **Docker Desktop:** The application must be running. You can download it from [Docker's official site](https://www.docker.com/products/docker-desktop).
-   **WSL2 (Windows Subsystem for Linux):** You must have a Linux distribution like **Ubuntu** installed from the Microsoft Store.
-   **Enable WSL Integration:** In your Docker Desktop settings, you must enable WSL Integration for your chosen distro.
    -   *Navigate to: Settings -> Resources -> WSL Integration.*
    -   *Turn the toggle **ON** for your distro (e.g., Ubuntu).*

#### **Step 2: Clone & Install**
All of the following commands must be run inside the **WSL Terminal** (e.g., Ubuntu), not Windows Command Prompt or PowerShell.

1.  **Open your WSL Terminal.**
2.  **Install Python:** Your WSL environment needs its own copy of Python 3 and its package installer, `pip`.
    ```bash
    sudo apt update
    sudo apt install python3-pip -y
    ```
3.  **Clone the Repository:**
    ```bash
    # Go to your WSL home directory
    cd ~
    # Clone this specific repository
    git clone https://github.com/Gregory-307/hummingbot_batched_experiments.git
    # Navigate into the newly cloned project folder
    cd hummingbot_batched_experiments
    ```
4.  **Install Python Dependencies:**
    ```bash
    python3 -m pip install requests pandas streamlit ruamel.yaml
    ```
5.  **Place Your Custom Strategies:** Your custom `.py` strategy files should be placed in the following directory:
    `hummingbot_files/strategies/`

### **macOS Setup (Untested)**

#### **Step 1: Install Prerequisites**
-   **Docker Desktop for Mac:** The application must be running. You can download it from [Docker's official site](https://www.docker.com/products/docker-desktop).
-   **Homebrew:** A package manager for macOS. If you don't have it, open Terminal and run:
    ```bash
    /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
    ```
-   **Python 3:** Install using Homebrew.
    ```bash
    brew install python3
    ```

#### **Step 2: Clone & Install**
1.  **Open the standard Terminal app.**
2.  **Clone the Repository:**
    ```bash
    # Go to your home directory
    cd ~
    # Clone this specific repository
    git clone https://github.com/Gregory-307/hummingbot_batched_experiments.git
    # Navigate into the newly cloned project folder
    cd hummingbot_batched_experiments
    ```
3.  **Install Python Dependencies:**
    ```bash
    python3 -m pip install requests pandas streamlit ruamel.yaml
    ```
4.  **Place Your Custom Strategies:** Your custom `.py` strategy files should be placed in the following directory:
    `hummingbot_files/strategies/custom/`

---

## How to Run: Multi-Terminal Workflow

This system is designed to be run using two separate WSL terminals.

### **Terminal 1: Start Core Hummingbot Services**  (main Hummingbot services)  

1.  **Navigate to the project directory** (e.g., `cd ~/hummingbot_batched_experiments`).  
   
2.  **First-Time Setup:** Run this command only the first time you set up the project, or when you want to download new updates from Hummingbot. Otherwise run the commands in **Daily Workflow**   
    ```bash
    bash setup.sh\n
    ```

    You should see something like this: 
    ```
    ###############:~/hummingbot_batched_experiments$ bash setup.sh
    [+] Pulling 9/111
    ⠸ dashboard [⣿⣿⣿⣿⣿⣿⣿⣿] 386.9MB / 388.4MB Pulling
    ```
    If nothing prints go to troubleshooting.  
    
4.  **Verify Services are Running:** After the script finishes, check that everything is running correctly:
    ```bash
    docker ps
    ```
    You should see a list of running containers, including `deploy-backend-api-1` and `deploy-dashboard-1`.

5.  **Access Services:** You can access the following services in your browser:
    -   **Main Dashboard UI:** `http://localhost:8501`
    -   **Backend API Docs:** `http://localhost:8000/docs`
  
### Troubleshooting: If `bash setup.sh` is stuck and shows no output

-   **Symptom:** You run `bash setup.sh` in your WSL terminal, but the screen is blank for more than a minute. You do not see any of the Docker download progress bars.
-   **Cause:** This almost always means the connection between Docker Desktop and your WSL environment has failed or stalled.
-   **Solution (The "Hard Reset"):**
    1.  Close all open WSL terminals.
    2.  Quit Docker Desktop from the Windows system tray (right-click the whale icon -> "Quit Docker Desktop").
    3.  Open **Windows PowerShell** (not WSL/Ubuntu).
    4.  In PowerShell, run this command to force a full shutdown of the WSL service:
        ```
        wsl --shutdown
        ```
    5.  Restart the **Docker Desktop** application. Wait for it to fully initialize (the engine status in the bottom-left should be green).
    6.  Open a new WSL terminal and navigate back to your project directory to try steps 1 and 2 again.


#### **Daily Workflow**

After you have completed the first-time setup, **you do not need to run `bash setup.sh` again**. Use these commands to start and stop the services.

-   **To START the services:**
    ```bash
    # From your project directory, e.g., ~/hummingbot_batched_experiments
    docker compose up -d
    ```
-   **To STOP the services:**
    ```bash
    # From your project directory
    docker compose down
    ```
    

### **Terminal 2: Run Experiments & Analyze Results**

**Navigate to the project directory** in a new terminal window. This terminal is your workspace for the custom experimentation framework.   

The Experimentation framework is split into two 3 stages (and 3 easy commands):  
  
1.  Broad Model and Configuration Testing  
        - Many different experiments (different models/configurations) tested on 3 specific backtests  
        - ```bash
            python3 hummingbot_files/experiment_runner.py
          ```  
        - Results will be recorded to `backtest_results.csv` - **This file is cumulative and will grow overtime.**  
        - **To edit your experiments:** Open the file `hummingbot_files/experiment_runner.py` in your code editor to define the batch of backtests you want to run.  
*Every experiment has a unique hash. If two experiments are the same, they will also have the same hash. `experiment_runner.py` will skip experiments with an existing hash in `backtest_results.csv` to save time.*  
        
2.  Deep Analysis  
        - ```bash
            python3 hummingbot_files/deep_analysis.py
          ```  
        - The top 5 models (by PnL as default) will be tested over many different ranges and timeframe configurations.  
        - Results will be recorded to `top_model_results.csv`  **Warning this file is not cumulative and will be overwritten**  
     
3.  **Analyze Experiment Results:** Once the experiments are finished, start the custom analysis dashboard.  
    ```bash
    streamlit run hummingbot_files/dashboard.py
    ```  
    Check the terminal output for the exact URL. It will typically be on the next available port, such as **`http://localhost:8502`**.  


---

## Key Directory Structure

-   `docker-compose.yml`: The blueprint that defines all the core services and their configurations.
-   `hummingbot_files/`: **This is your primary workspace for custom development.**
    -   `strategies/custom/`: Your custom strategy files live here.
    -   `experiment_runner.py`: The script that orchestrates the backtesting experiments. **This is the main file to edit.**
    -   `dashboard.py`: The Streamlit dashboard script for visualizing results.
    -   `results/`: Default location for the `backtest_results.csv` output.
