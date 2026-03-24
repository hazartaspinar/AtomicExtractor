<#
.SYNOPSIS
    Extracts Atomic Red Team techniques and tests into a structured JSON dataset for AI/ML training.

.DESCRIPTION
    This script iterates through the Atomic Red Team library, extracts Technique IDs, Names, 
    Test Payloads, and Descriptions, then saves them as a clean JSON file. 
    It handles missing technique names and cleans up control characters for better AI processing.

.AUTHOR
    Hazar (Offensive Security Consultant)
#>

# 1. Configuration & Module Loading
$atomicsPath = "C:\AtomicRedTeam\atomics"
$outputFile = "$HOME\Desktop\Atomic_Red_Team_AI_Dataset.json"

Write-Host "[*] Loading Invoke-AtomicRedTeam module..." -ForegroundColor Cyan
try {
    Import-Module "C:\AtomicRedTeam\Invoke-AtomicRedTeam\Invoke-AtomicRedTeam.psd1" -Force -ErrorAction Stop
} catch {
    Write-Error "Failed to load the module. Please check the path: $atomicsPath"
    exit
}

# 2. File Discovery
Write-Host "[*] Discovering YAML technique files..." -ForegroundColor Cyan
# Filters only actual Technique files (Txxxx.yaml) and ignores index files
$yamlFiles = Get-ChildItem -Path $atomicsPath -Filter "T*.yaml" -Recurse | Where-Object { $_.Name -notlike "*-index.yaml" }

$dataset = @()

# 3. Data Processing Loop
Write-Host "[*] Processing $($yamlFiles.Count) files. This may take a minute..." -ForegroundColor Cyan

foreach ($file in $yamlFiles) {
    try {
        $technique = Get-AtomicTechnique -Path $file.FullName
        $techID = $file.BaseName 

        # Recovery Logic: Ensure Technique Name is not null
        $techName = $technique.technique_name
        if (-not $techName) { $techName = $technique.display_name }
        if (-not $techName -or $techName -eq "Unknown") {
            # Manual fallback from YAML header if module metadata is missing
            $techName = "MITRE Technique $techID"
        }

        foreach ($test in $technique.atomic_tests) {
            # Data Cleaning: Remove newlines and carriage returns for clean JSON
            $cleanPayload = $test.executor.command -replace "`n", " " -replace "`r", ""
            $cleanDesc = $test.description -replace "`n", " " -replace "`r", ""

            $entry = [PSCustomObject]@{
                TechniqueID   = $techID
                TechniqueName = $techName
                TestName      = $test.name
                Platform      = ($test.supported_platforms -join ", ")
                Payload       = $cleanPayload
                Description   = $cleanDesc
            }
            $dataset += $entry
        }
    } catch {
        # Silent continue for non-conforming YAML files to keep output clean
        continue 
    }
}

# 4. Exporting Data
Write-Host "[*] Exporting results to JSON..." -ForegroundColor Cyan
$dataset | ConvertTo-Json -Depth 10 | Out-File $outputFile -Encoding utf8

Write-Host "`n[SUCCESS] Extraction complete!" -ForegroundColor Green
Write-Host "[+] Total Samples: $($dataset.Count)" -ForegroundColor Green
Write-Host "[+] Dataset saved to: $outputFile" -ForegroundColor Yellow
