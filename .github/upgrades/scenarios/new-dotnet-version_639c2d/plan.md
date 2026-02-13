# .NET 10.0 Upgrade Plan for BTCPayServer Solution

**Date**: February 13, 2026  
**Target Framework**: .NET 10.0 (Long Term Support)  
**Current Framework**: .NET 8.0 (most projects), netstandard2.1 (BTCPayServer.Client)  
**Strategy**: All-At-Once

---

## Executive Summary

### Selected Strategy
**All-At-Once Strategy** - All projects upgraded simultaneously in a single coordinated operation.

**Rationale**: 
- **8 projects** (small solution, ideal for atomic upgrade)
- All projects currently on .NET 8.0 or netstandard2.1 (modern frameworks)
- Clear, manageable dependency structure with no circular dependencies
- All required packages have .NET 10.0-compatible versions available
- **82.4% of packages already compatible** with .NET 10.0
- Only **9 packages require updates** (17.6%)
- All projects are SDK-style (modern project format)
- **Low complexity**: All projects rated 🟢 Low difficulty
- Strong foundation for atomic upgrade

**Benefits of All-At-Once**:
- Fastest completion timeline (single coordinated operation)
- No multi-targeting complexity
- Clean dependency resolution
- All projects benefit from .NET 10 features immediately
- Single comprehensive test cycle

---

## Current State Overview

### Projects Summary

| Project | Current TFM | Type | Package Issues | API Issues | Complexity |
|---------|-------------|------|----------------|------------|------------|
| BTCPayServer.Client | netstandard2.1 | Library | 1 | 39 behavioral | 🟢 Low |
| BTCPayServer.Common | net8.0 | Library | 0 | 0 | 🟢 Low |
| BTCPayServer.Abstractions | net8.0 | Library | 1 | 0 | 🟢 Low |
| BTCPayServer.Rating | net8.0 | Library | 2 | 0 | 🟢 Low |
| BTCPayServer.Data | net8.0 | Library | 2 | 0 | 🟢 Low |
| BTCPayServer | net8.0 | Web App | 6 | 0 | 🟢 Low |
| BTCPayServer.PluginPacker | net8.0 | CLI Tool | 0 | 0 | 🟢 Low |
| BTCPayServer.Tests | net8.0 | Tests | 1 | 0 | 🟢 Low |

**Total**: 8 projects, 170,246 LOC, 1,550 code files

---

## Dependency Analysis

### Project Dependency Graph

```
BTCPayServer.PluginPacker → BTCPayServer → {Abstractions, Common, Data, Client, Rating}
                                           ↓
BTCPayServer.Tests → BTCPayServer         Abstractions → Client
                  → Rating                 Data → {Client, Rating}
                                          Rating → Client
```

### Dependency Levels

**Level 0 (Leaf Nodes - No Internal Dependencies)**:
- BTCPayServer.Client
- BTCPayServer.Common

**Level 1 (Depends on Level 0)**:
- BTCPayServer.Abstractions (→ Client)
- BTCPayServer.Rating (→ Client)

**Level 2 (Depends on Level 0-1)**:
- BTCPayServer.Data (→ Abstractions, Client, Rating)

**Level 3 (Main Application)**:
- BTCPayServer (→ All above)

**Level 4 (Tools & Tests)**:
- BTCPayServer.PluginPacker (→ BTCPayServer)
- BTCPayServer.Tests (→ BTCPayServer, Rating)

**All-At-Once Strategy Note**: While this shows logical dependency order for understanding, all projects will be upgraded simultaneously in a single operation.

---

## Package Update Reference

### Packages Requiring Updates (9 packages)

| Package | Current | Target | Projects Affected | Priority |
|---------|---------|--------|-------------------|----------|
| **Microsoft.EntityFrameworkCore** | 8.0.11 | 10.0.3 | BTCPayServer.Abstractions | High |
| **Microsoft.EntityFrameworkCore.Design** | 8.0.11 | 10.0.3 | BTCPayServer.Data | High |
| **Microsoft.AspNetCore.Identity.EntityFrameworkCore** | 8.0.11 | 10.0.3 | BTCPayServer.Data | High |
| **Microsoft.AspNetCore.Mvc.NewtonsoftJson** | 8.0.11 | 10.0.3 | BTCPayServer | High |
| **Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation** | 8.0.11 | 10.0.3 | BTCPayServer, BTCPayServer.Tests | High |
| **Microsoft.AspNetCore.SignalR.Protocols.NewtonsoftJson** | 8.0.11 | 10.0.3 | BTCPayServer | High |
| **Npgsql.EntityFrameworkCore.PostgreSQL** | 8.0.11 | 10.0.3 | BTCPayServer.Data | High |
| **System.IO.Pipelines** | 8.0.0 | 10.0.3 | BTCPayServer | High |
| **System.Text.Json** | 8.0.5 | 10.0.3 | BTCPayServer.Rating | High |

### Packages Compatible (42 packages)
All other packages (82.4%) are already compatible with .NET 10.0 and require no updates.

### Package to Remove (1 package)
- **System.Text.RegularExpressions** (BTCPayServer): Functionality now included in framework

---

## Breaking Changes Catalog

### API Compatibility Analysis

**Impact Summary**:
- 🔴 Binary Incompatible: **0** (none)
- 🟡 Source Incompatible: **0** (none)
- 🔵 Behavioral Changes: **39** (all in BTCPayServer.Client)
- ✅ Compatible APIs: **34,592**

### Behavioral Changes to Monitor

All 39 behavioral changes are in **BTCPayServer.Client** project and relate to:

**1. System.Uri (32 occurrences - 82.1%)**
- URI parsing and formatting may have subtle behavior changes
- **Action**: Review Uri construction and comparison logic
- **Testing**: Validate URL generation and parsing in API client

**2. System.Net.Http.HttpContent (7 occurrences - 17.9%)**
- HTTP content handling behavior changes
- **Action**: Review HTTP request/response content handling
- **Testing**: Validate HTTP client operations

**Mitigation Strategy**:
- Focus testing on BTCPayServer.Client HTTP operations
- Validate API client functionality thoroughly
- Monitor for any URL/URI-related issues in integration tests

### Framework-Level Changes (.NET 8 → .NET 10)

**Expected Changes**:
1. **Performance Improvements**: General runtime optimizations
2. **Nullability Annotations**: Enhanced null reference type annotations in BCL
3. **API Additions**: New .NET 10 APIs available
4. **Obsolete APIs**: Some .NET 8 APIs may be deprecated

**Action Items**:
- Monitor compiler warnings for obsolete API usage
- Address any new nullability warnings
- Leverage new .NET 10 features where beneficial

---

## Implementation Timeline

### Phase 0: Preparation (Estimated: 15 minutes)

**Prerequisites**:
- ✅ Verify .NET 10 SDK installed: `dotnet --list-sdks`
- ✅ Check for global.json constraints (if present)
- ✅ Ensure clean git state on upgrade branch

**Deliverables**: Development environment ready

---

### Phase 1: Atomic Upgrade (Estimated: 45-60 minutes)

**Operations** (performed as single coordinated batch):

**1.1 Update All Project Target Frameworks**
Update `TargetFramework` property in all 8 project files:
- BTCPayServer.Client: `netstandard2.1` → `net10.0`
- BTCPayServer.Common: `net8.0` → `net10.0`
- BTCPayServer.Abstractions: `net8.0` → `net10.0`
- BTCPayServer.Rating: `net8.0` → `net10.0`
- BTCPayServer.Data: `net8.0` → `net10.0`
- BTCPayServer: `net8.0` → `net10.0`
- BTCPayServer.PluginPacker: `net8.0` → `net10.0`
- BTCPayServer.Tests: `net8.0` → `net10.0`

**1.2 Update All Package References**

Update 9 packages across projects:
- **BTCPayServer.Abstractions**: Microsoft.EntityFrameworkCore 8.0.11 → 10.0.3
- **BTCPayServer.Data**: 
  - Microsoft.EntityFrameworkCore.Design 8.0.11 → 10.0.3
  - Microsoft.AspNetCore.Identity.EntityFrameworkCore 8.0.11 → 10.0.3
  - Npgsql.EntityFrameworkCore.PostgreSQL 8.0.11 → 10.0.3
- **BTCPayServer**:
  - Microsoft.AspNetCore.Mvc.NewtonsoftJson 8.0.11 → 10.0.3
  - Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation 8.0.11 → 10.0.3
  - Microsoft.AspNetCore.SignalR.Protocols.NewtonsoftJson 8.0.11 → 10.0.3
  - System.IO.Pipelines 8.0.0 → 10.0.3
  - Remove System.Text.RegularExpressions (now in framework)
- **BTCPayServer.Rating**: System.Text.Json 8.0.5 → 10.0.3
- **BTCPayServer.Tests**: Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation 8.0.11 → 10.0.3

**1.3 Restore Dependencies**
```bash
dotnet restore btcpayserver.sln
```

**1.4 Build Solution**
```bash
dotnet build btcpayserver.sln --no-restore
```

**1.5 Fix Compilation Errors (if any)**
- Address any breaking API changes discovered during build
- Fix namespace changes
- Update obsolete API usage
- Resolve any new nullability warnings

**Expected Issues**:
- Minimal to none (all compatibility metrics are favorable)
- Focus on BTCPayServer.Client if Uri/HttpContent issues arise

**Deliverables**: 
- ✅ Solution builds with 0 errors
- ✅ Compilation warnings reviewed and addressed
- ✅ All 8 projects target .NET 10.0

---

### Phase 2: Test Validation (Estimated: 30-45 minutes)

**Operations**:

**2.1 Run Full Test Suite**
```bash
dotnet test BTCPayServer.Tests/BTCPayServer.Tests.csproj --no-build
```

**2.2 Address Test Failures (if any)**
- Investigate failures related to behavioral changes (Uri, HttpContent)
- Fix test assertions affected by framework changes
- Validate no functionality regressions

**2.3 Focused Testing for Behavioral Changes**
- **BTCPayServer.Client**: Validate API client operations
  - URL/URI construction and parsing
  - HTTP request/response handling
  - API endpoint communication
- **Integration Tests**: End-to-end scenarios
- **Performance Testing**: Ensure no performance regressions

**Deliverables**: 
- ✅ All tests pass
- ✅ Behavioral changes validated
- ✅ No functionality regressions

---

### Phase 3: Final Verification (Estimated: 15-30 minutes)

**Operations**:

**3.1 Code Quality Checks**
- Run any existing linters/analyzers
- Review compiler warnings
- Address any new code analysis issues

**3.2 Build Validation**
- Clean build from scratch:
  ```bash
  dotnet clean btcpayserver.sln
  dotnet restore btcpayserver.sln
  dotnet build btcpayserver.sln
  ```

**3.3 Documentation Updates**
- Update README.md with .NET 10.0 requirements
- Update build documentation
- Update contributor guidelines if needed

**Deliverables**:
- ✅ Clean build with zero warnings
- ✅ Documentation updated
- ✅ Code quality verified

---

## Detailed Execution Steps

### Step 1: Update Project Files (All 8 Projects Simultaneously)

For each `.csproj` file, update the `<TargetFramework>` element:

**Projects targeting net8.0 → net10.0** (7 projects):
```xml
<!-- FROM -->
<TargetFramework>net8.0</TargetFramework>

<!-- TO -->
<TargetFramework>net10.0</TargetFramework>
```

**Project targeting netstandard2.1 → net10.0** (BTCPayServer.Client):
```xml
<!-- FROM -->
<TargetFramework>netstandard2.1</TargetFramework>

<!-- TO -->
<TargetFramework>net10.0</TargetFramework>
```

**Files to modify**:
1. `BTCPayServer.Abstractions/BTCPayServer.Abstractions.csproj`
2. `BTCPayServer.Client/BTCPayServer.Client.csproj`
3. `BTCPayServer.Common/BTCPayServer.Common.csproj`
4. `BTCPayServer.Data/BTCPayServer.Data.csproj`
5. `BTCPayServer.PluginPacker/BTCPayServer.PluginPacker.csproj`
6. `BTCPayServer.Rating/BTCPayServer.Rating.csproj`
7. `BTCPayServer.Tests/BTCPayServer.Tests.csproj`
8. `BTCPayServer/BTCPayServer.csproj`

---

### Step 2: Update Package References

Update `<PackageReference>` elements in project files:

**BTCPayServer.Abstractions/BTCPayServer.Abstractions.csproj**:
```xml
<PackageReference Include="Microsoft.EntityFrameworkCore" Version="10.0.3" />
```

**BTCPayServer.Data/BTCPayServer.Data.csproj**:
```xml
<PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="10.0.3" />
<PackageReference Include="Microsoft.AspNetCore.Identity.EntityFrameworkCore" Version="10.0.3" />
<PackageReference Include="Npgsql.EntityFrameworkCore.PostgreSQL" Version="10.0.3" />
```

**BTCPayServer/BTCPayServer.csproj**:
```xml
<PackageReference Include="Microsoft.AspNetCore.Mvc.NewtonsoftJson" Version="10.0.3" />
<PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="10.0.3" />
<PackageReference Include="Microsoft.AspNetCore.SignalR.Protocols.NewtonsoftJson" Version="10.0.3" />
<PackageReference Include="System.IO.Pipelines" Version="10.0.3" />
<!-- REMOVE: <PackageReference Include="System.Text.RegularExpressions" Version="4.3.1" /> -->
```

**BTCPayServer.Rating/BTCPayServer.Rating.csproj**:
```xml
<PackageReference Include="System.Text.Json" Version="10.0.3" />
```

**BTCPayServer.Tests/BTCPayServer.Tests.csproj**:
```xml
<PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="10.0.3" />
```

---

### Step 3: Build and Validate

**3.1 Restore Dependencies**
```bash
dotnet restore btcpayserver.sln
```
- Verify all packages restore successfully
- Check for any package conflict warnings

**3.2 Build Solution**
```bash
dotnet build btcpayserver.sln --configuration Release
```
- Monitor for compilation errors
- Review all warnings
- Address issues systematically

**3.3 Expected Build Issues**

**Likely Issues** (if any):
- Obsolete API warnings (update to new APIs)
- Nullability warnings (add null checks or annotations)
- Uri/HttpContent usage (in BTCPayServer.Client)

**Resolution Approach**:
- Address compilation errors first
- Review and fix warnings
- Focus on BTCPayServer.Client for behavioral changes

---

### Step 4: Test Execution

**4.1 Run Test Suite**
```bash
dotnet test BTCPayServer.Tests/BTCPayServer.Tests.csproj --configuration Release
```

**4.2 Analyze Test Results**
- Identify failing tests
- Categorize by root cause
- Prioritize critical failures

**4.3 Test Categories to Validate**

**Critical**:
- API client tests (BTCPayServer.Client usage)
- HTTP request/response tests
- URL/URI parsing tests
- Integration tests

**Standard**:
- Unit tests
- Service tests
- Database tests

**4.4 Fix Test Failures**
- Update test assertions affected by behavioral changes
- Fix Uri/HttpContent-related test issues
- Validate no functionality regressions

---

## Risk Management

### Risk Assessment by Project

| Project | Risk Level | Rationale | Mitigation |
|---------|------------|-----------|------------|
| BTCPayServer.Client | 🟡 Medium | 39 behavioral changes (Uri, HttpContent) | Focused API testing, manual validation |
| BTCPayServer | 🟡 Medium | Main app, 6 package updates | Comprehensive integration testing |
| BTCPayServer.Data | 🟢 Low | EF Core upgrade, well-tested ORM | Database migration validation |
| BTCPayServer.Tests | 🟢 Low | Test project, 1 package update | Self-validating through test execution |
| BTCPayServer.Rating | 🟢 Low | 2 package updates, no API issues | Standard unit testing |
| BTCPayServer.Abstractions | 🟢 Low | 1 package update, no API issues | Dependency validation |
| BTCPayServer.Common | 🟢 Low | No package updates, no API issues | Basic compilation validation |
| BTCPayServer.PluginPacker | 🟢 Low | CLI tool, no package updates | Tool functionality testing |

### Overall Risk Level: 🟢 Low to Medium

**Risk Factors**:
- ✅ All projects SDK-style (modern format)
- ✅ Small solution (8 projects)
- ✅ High package compatibility (82.4%)
- ✅ No binary breaking changes
- ✅ No source incompatibilities
- ⚠️ 39 behavioral changes in Client project
- ✅ All packages have .NET 10 versions

---

### Mitigation Strategies

**For BTCPayServer.Client Behavioral Changes**:
1. **Focused Testing**: Extensive API client testing
2. **Code Review**: Review all Uri and HttpContent usage
3. **Integration Tests**: Validate real API communication
4. **Documentation**: Document any behavioral differences found

**For Entity Framework Updates**:
1. **Database Backup**: Backup database before testing
2. **Migration Validation**: Ensure EF Core 10.0 migrations work
3. **Query Testing**: Validate LINQ queries still work correctly

**For General Upgrade**:
1. **Incremental Validation**: Build after each change category
2. **Test Early**: Run tests as soon as build succeeds
3. **Monitor Warnings**: Address all compiler warnings
4. **Performance Testing**: Validate no performance regressions

---

### Rollback Plan

**If critical issues discovered**:

**Option 1: Revert Entire Upgrade**
```bash
git reset --hard HEAD~1
git push --force-with-lease
```

**Option 2: Selective Revert**
- Identify problematic project/package
- Revert specific changes
- Re-test affected areas

**Option 3: Hot-fix Forward**
- Apply targeted fixes
- Re-run affected tests
- Deploy fix with upgrade

**Recommendation**: Due to All-At-Once strategy, Option 1 (full revert) is simplest if major issues found.

---

## Testing Strategy

### Multi-Level Testing Approach

**Level 1: Compilation Validation**
- ✅ All projects build without errors
- ✅ All projects build without warnings
- ✅ Package references resolve correctly

**Level 2: Unit Testing**
- ✅ All unit tests pass
- ✅ No test regressions
- ✅ Code coverage maintained

**Level 3: Integration Testing**
- ✅ API client operations validated
- ✅ Database operations functional
- ✅ HTTP request/response handling correct
- ✅ Plugin system operational

**Level 4: System Testing**
- ✅ Full application starts successfully
- ✅ End-to-end scenarios functional
- ✅ Performance acceptable
- ✅ No runtime errors

---

### Testing Checklist

**Pre-Upgrade**:
- [ ] Capture baseline test results (all tests passing)
- [ ] Document current performance metrics
- [ ] Verify test environment ready

**Post-Upgrade - BTCPayServer.Client** (High Focus):
- [ ] API client instantiation works
- [ ] HTTP requests succeed
- [ ] URL/URI construction correct
- [ ] Response parsing functional
- [ ] Error handling works
- [ ] Authentication flows succeed

**Post-Upgrade - BTCPayServer**:
- [ ] Application starts without errors
- [ ] Routing works correctly
- [ ] Database connections succeed
- [ ] SignalR connections functional
- [ ] Razor compilation works
- [ ] Plugin loading succeeds

**Post-Upgrade - BTCPayServer.Data**:
- [ ] EF Core migrations compatible
- [ ] Database queries execute correctly
- [ ] Entity relationships intact
- [ ] Database transactions work

**Post-Upgrade - BTCPayServer.Tests**:
- [ ] All unit tests pass
- [ ] All integration tests pass
- [ ] Test infrastructure functional
- [ ] Code coverage maintained

**Post-Upgrade - Other Projects**:
- [ ] BTCPayServer.Rating: Rating calculations correct
- [ ] BTCPayServer.Common: Utility functions work
- [ ] BTCPayServer.Abstractions: Interfaces unchanged
- [ ] BTCPayServer.PluginPacker: Plugin packaging works

---

## Source Control Strategy

### Commit Strategy

**Recommended: Single Atomic Commit**

All changes committed together as one cohesive upgrade:

```bash
# After all changes complete and tests pass
git add .
git commit -m "Upgrade entire solution to .NET 10.0

- Update all 8 projects from net8.0/netstandard2.1 to net10.0
- Upgrade 9 packages to .NET 10-compatible versions:
  - Microsoft.EntityFrameworkCore: 8.0.11 → 10.0.3
  - Microsoft.EntityFrameworkCore.Design: 8.0.11 → 10.0.3
  - Microsoft.AspNetCore.Identity.EntityFrameworkCore: 8.0.11 → 10.0.3
  - Microsoft.AspNetCore.Mvc.NewtonsoftJson: 8.0.11 → 10.0.3
  - Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation: 8.0.11 → 10.0.3
  - Microsoft.AspNetCore.SignalR.Protocols.NewtonsoftJson: 8.0.11 → 10.0.3
  - Npgsql.EntityFrameworkCore.PostgreSQL: 8.0.11 → 10.0.3
  - System.IO.Pipelines: 8.0.0 → 10.0.3
  - System.Text.Json: 8.0.5 → 10.0.3
- Remove System.Text.RegularExpressions (now in framework)
- All tests passing
- No breaking changes required"

git push origin copilot/upgrade-btcpayserver-to-net10
```

**Benefits**:
- Clean, atomic upgrade
- Easy to revert if needed
- Clear history
- All-At-Once strategy reflected in commit structure

**Alternative: Phased Commits** (if issues arise):
1. Commit: Update project files
2. Commit: Update package references
3. Commit: Fix compilation errors (if any)
4. Commit: Fix test failures (if any)

---

## Success Criteria

### The upgrade is complete when:

**Technical Criteria**:
- ✅ All 8 projects target .NET 10.0
- ✅ All 9 package updates applied
- ✅ System.Text.RegularExpressions removed
- ✅ Solution builds with 0 errors
- ✅ Solution builds with 0 warnings (or only acceptable warnings)
- ✅ All tests pass (100% pass rate)
- ✅ No package dependency conflicts
- ✅ No runtime errors during smoke testing

**Quality Criteria**:
- ✅ Code quality maintained
- ✅ Test coverage maintained
- ✅ Performance acceptable (no regressions)
- ✅ Behavioral changes validated

**Documentation Criteria**:
- ✅ README.md updated with .NET 10 requirements
- ✅ Build documentation updated
- ✅ Contributors notified of framework change

**Deployment Criteria**:
- ✅ Changes committed to upgrade branch
- ✅ Pull request created
- ✅ Code review completed
- ✅ CI/CD pipeline passes

---

## Timeline Estimate

### Total Estimated Time: 2-3 hours

| Phase | Estimated Time | Activity |
|-------|----------------|----------|
| Preparation | 15 min | SDK verification, environment setup |
| Project Files Update | 10 min | Update 8 .csproj files |
| Package Updates | 10 min | Update 9 packages, remove 1 package |
| First Build | 15 min | Restore & build, fix immediate errors |
| Compilation Fixes | 15-30 min | Fix any breaking changes |
| Test Execution | 20-30 min | Run full test suite |
| Test Fixes | 10-20 min | Fix test failures (if any) |
| Validation | 15 min | Final verification, smoke testing |
| Documentation | 10 min | Update docs |
| Commit & PR | 5 min | Git operations |

**Factors affecting timeline**:
- ⬆️ If behavioral changes cause issues: +30-60 min
- ⬆️ If unexpected breaking changes: +60-120 min
- ⬇️ If all builds clean: -30 min
- ⬇️ If all tests pass first try: -15 min

---

## Post-Upgrade Tasks

### Immediate Follow-up
1. Monitor application in development environment
2. Validate all critical user flows
3. Check logs for any runtime warnings
4. Verify plugin system functionality

### Short-term Follow-up (within 1 week)
1. Deploy to staging environment
2. Run full regression test suite
3. Performance testing and profiling
4. Monitor for any issues in staging

### Long-term Follow-up (within 1 month)
1. Consider leveraging new .NET 10 features
2. Update development guidelines
3. Plan for removal of deprecated patterns
4. Evaluate performance improvements

---

## Communication Plan

### Stakeholder Notifications

**Development Team**:
- Notify of framework upgrade completion
- Share any breaking changes or new patterns
- Update development environment setup instructions

**QA Team**:
- Provide detailed list of behavioral changes
- Highlight areas needing focused testing
- Share test execution results

**DevOps Team**:
- Update deployment documentation
- Verify CI/CD pipeline compatibility
- Update container images (if using Docker)

**Documentation**:
- Update README.md
- Update wiki/documentation
- Update contributor guidelines

---

## Appendix

### Related Documentation

- [.NET 10 Release Notes](https://learn.microsoft.com/en-us/dotnet/core/whats-new/dotnet-10)
- [Breaking Changes in .NET 10](https://learn.microsoft.com/en-us/dotnet/core/compatibility/10.0)
- [Migration Guide: .NET 8 to .NET 10](https://learn.microsoft.com/en-us/dotnet/core/migration/)
- [Entity Framework Core 10.0 What's New](https://learn.microsoft.com/en-us/ef/core/what-is-new/ef-core-10.0/whatsnew)

### Tools and Resources

**Required Tools**:
- .NET 10 SDK ([Download](https://dotnet.microsoft.com/download/dotnet/10.0))
- Compatible IDE (Visual Studio 2025, VS Code, Rider)

**Helpful Tools**:
- [.NET Upgrade Assistant](https://dotnet.microsoft.com/platform/upgrade-assistant)
- [API Analyzer](https://learn.microsoft.com/en-us/dotnet/standard/analyzers/)
- [Code Analysis Rules](https://learn.microsoft.com/en-us/dotnet/fundamentals/code-analysis/overview)

---

## Approval

This plan is ready for review and execution. Once approved, the Execution stage will implement these changes systematically with progress tracking.

**Next Steps**:
1. Review this plan for completeness
2. Approve plan and proceed to Execution stage
3. Execute upgrade following this blueprint
4. Track progress through tasks.md in Execution stage

---

*This plan supports systematic execution of the .NET 10.0 upgrade with clear success criteria, risk mitigation, and validation checkpoints.*
