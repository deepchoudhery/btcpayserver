# BTCPayServer .NET 10.0 Upgrade Tasks

## Overview

This document tracks the execution of the BTCPayServer solution upgrade from .NET 8.0 to .NET 10.0. All 8 projects will be upgraded simultaneously in a single atomic operation following the All-At-Once strategy.

**Progress**: 0/4 tasks complete (0%) ![0%](https://progress-bar.xyz/0)

---

## Tasks

### [ ] TASK-001: Verify prerequisites and environment setup
**References**: #plan:Phase-0-Preparation

- [ ] (1) Verify .NET 10 SDK is installed
- [ ] (2) .NET 10 SDK version meets minimum requirements (**Verify**)
- [ ] (3) Check for global.json file and validate SDK version constraints (if present)
- [ ] (4) Configuration files compatible with .NET 10.0 (**Verify**)

### [ ] TASK-002: Atomic framework and package upgrade
**References**: #plan:Phase-1-Atomic-Upgrade, #plan:Detailed-Execution-Steps, #plan:Package-Update-Reference, #plan:Breaking-Changes-Catalog

- [ ] (1) Update TargetFramework to net10.0 in all 8 project files per #plan:Step-1-Update-Project-Files
- [ ] (2) All project files updated to net10.0 (**Verify**)
- [ ] (3) Update package references per #plan:Step-2-Update-Package-References (key packages: EF Core 10.0.3, ASP.NET Core 10.0.3, Npgsql 10.0.3)
- [ ] (4) Remove System.Text.RegularExpressions package from BTCPayServer project (now in framework)
- [ ] (5) All package references updated correctly (**Verify**)
- [ ] (6) Restore all NuGet dependencies for the solution
- [ ] (7) All dependencies restored successfully with no conflicts (**Verify**)
- [ ] (8) Build solution and fix all compilation errors per #plan:Breaking-Changes-Catalog
- [ ] (9) Solution builds with 0 errors (**Verify**)

### [ ] TASK-003: Execute full test suite and validate behavioral changes
**References**: #plan:Phase-2-Test-Validation, #plan:Testing-Strategy

- [ ] (1) Run all tests in BTCPayServer.Tests project
- [ ] (2) Fix any test failures related to behavioral changes (reference #plan:Breaking-Changes-Catalog for Uri and HttpContent issues)
- [ ] (3) Re-run tests after fixes
- [ ] (4) All tests pass with 0 failures (**Verify**)

### [ ] TASK-004: Final verification and commit all changes
**References**: #plan:Phase-3-Final-Verification, #plan:Source-Control-Strategy

- [ ] (1) Perform clean build from scratch (clean, restore, build)
- [ ] (2) Solution builds successfully with 0 errors and 0 warnings (**Verify**)
- [ ] (3) Update README.md with .NET 10.0 requirements
- [ ] (4) Update build documentation with new framework version
- [ ] (5) All documentation updated correctly (**Verify**)
- [ ] (6) Commit all changes with message: "Upgrade entire solution to .NET 10.0"

---
