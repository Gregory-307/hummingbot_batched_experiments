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

This system is designed to be run using two separate terminals.

### **Terminal 1: Start Core Hummingbot Services**

This terminal runs the main Hummingbot services in the background.

1.  **Navigate to the project directory** (e.g., `cd ~/hummingbot_batched_experiments`).
2.  **Start Docker Services:** This command starts the main dashboard and the backend API using Docker. It will take several minutes on the first run.
    ```bash
    bash setup.sh
    ```
3.  **Leave this terminal running.** You can access the following services in your browser:
    -   **Main Dashboard UI:** `http://localhost:8501`
    -   **Backend API Docs:** `http://localhost:8000/docs`

### **Terminal 2: Run Experiments & Analyze Results**

This terminal is your workspace for the custom experimentation framework.

1.  **Navigate to the project directory** in a new terminal window.
2.  **(Optional) Edit Your Experiments:** Open the file `hummingbot_files/experiment_runner.py` in your code editor to define the batch of backtests you want to run.
3.  **Execute the Experiment Runner:**
    ```bash
    python3 hummingbot_files/experiment_runner.py
    ```
    This will run all your defined experiments and generate a `backtest_results.csv` file in the `hummingbot_files/results/` directory.
4.  **Analyze Experiment Results:** Once the runner is finished, start the custom analysis dashboard.
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
