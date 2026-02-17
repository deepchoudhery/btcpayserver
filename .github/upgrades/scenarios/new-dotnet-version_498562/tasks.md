# BTCPayServer .NET 10.0 Upgrade Tasks

## Overview

This document tracks the execution of the BTCPayServer upgrade from .NET 8.0 to .NET 10.0. All 8 projects will be upgraded simultaneously in a single atomic operation, followed by comprehensive testing and validation.

**Progress**: 0/4 tasks complete (0%) ![0%](https://progress-bar.xyz/0)

---

## Tasks

### [ ] TASK-001: Verify prerequisites and environment
**References**: #plan:Phase-1-Preparation

- [ ] (1) Verify .NET 10 SDK is installed on the system
- [ ] (2) .NET 10 SDK available and meets version requirements (Verify)
- [ ] (3) Verify working tree is clean with no uncommitted changes
- [ ] (4) Working tree is clean (Verify)

### [ ] TASK-002: Atomic framework and package upgrade
**References**: #plan:Phase-2-Framework-Updates, #plan:Package-Update-Strategy, #plan:Breaking-Changes-Analysis

- [ ] (1) Update target framework to net10.0 in all 8 project files per #plan:Project-by-Project-Upgrade-Plans
- [ ] (2) All project files updated to net10.0 target framework (Verify)
- [ ] (3) Update all 9 package references per #plan:Package-Update-Strategy (key packages: Microsoft.AspNetCore.*, Microsoft.EntityFrameworkCore.*, System.IO.Pipelines, System.Text.Json, Newtonsoft.Json)
- [ ] (4) All package references updated to .NET 10 compatible versions (Verify)
- [ ] (5) Remove System.Text.RegularExpressions package from BTCPayServer.csproj per #plan:Packages-to-Remove
- [ ] (6) Redundant package removed (Verify)
- [ ] (7) Commit changes with message: "TASK-002: Atomic framework and package upgrade to .NET 10"

### [ ] TASK-003: Build verification and compilation fixes
**References**: #plan:Phase-3-Build-Verification, #plan:Breaking-Changes-Analysis

- [ ] (1) Restore all NuGet dependencies for the solution
- [ ] (2) All dependencies restored successfully (Verify)
- [ ] (3) Build entire solution and fix all compilation errors per #plan:Breaking-Changes-Analysis
- [ ] (4) Solution builds with 0 errors and 0 warnings (Verify)
- [ ] (5) Commit changes with message: "TASK-003: Build verification and fixes complete"

### [ ] TASK-004: Comprehensive testing and validation
**References**: #plan:Testing-Strategy, #plan:Success-Criteria

- [ ] (1) Run all tests in BTCPayServer.Tests project
- [ ] (2) Fix any test failures referencing #plan:Breaking-Changes-Analysis for behavioral changes
- [ ] (3) Re-run tests after fixes
- [ ] (4) All tests pass with 0 failures (Verify)
- [ ] (5) Commit changes with message: "TASK-004: All tests passing - upgrade complete"

---
