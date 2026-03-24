# Atomic-AI-Dataset-Maker

A minimalist PowerShell script to extract **Atomic Red Team** payloads into a structured JSON dataset for **AI/ML training**.

## Quick Start
1.  Open PowerShell as **Administrator**.
2.  Run the script:
    ```powershell
    .\AtomicExtractor.ps1
    ```
3.  Output: `Atomic_Red_Team_AI_Dataset.json` will be saved to your **Desktop**.

## Data Fields
- **TechniqueID / Name:** MITRE ATT&CK mapping.
- **Platform:** Target OS (Windows, Linux, macOS).
- **Payload:** The actual command-line execution string.
- **Description:** Context of the attack.

## Credits
Data sourced from the [Red Canary Atomic Red Team](https://github.com/redcanaryco/atomic-red-team) library.
