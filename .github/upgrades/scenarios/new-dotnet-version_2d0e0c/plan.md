# .NET 10 Upgrade Plan for BTCPayServer

**Date**: 2026-02-13  
**Target Framework**: .NET 10.0  
**Current Framework**: .NET 8.0  
**Strategy**: All-At-Once

---

## Executive Summary

This plan details the upgrade of the BTCPayServer solution from .NET 8.0 to .NET 10.0. The solution consists of 8 projects, all already in SDK-style format, making this a straightforward framework and package upgrade.

### Selected Strategy

**All-At-Once Strategy** - All projects upgraded simultaneously in a single coordinated operation.

**Rationale**:
- Small solution (8 projects total)
- All projects currently on .NET 8.0
- All projects already SDK-style
- Clear dependency structure
- All required packages have .NET 10-compatible versions
- Low risk ratings across all projects (🟢 Low)
- Good candidate for atomic upgrade

**Assessment Highlights**:
- **Total Projects**: 8 (all require upgrade)
- **Total NuGet Packages**: 51 (9 need upgrade, 42 compatible)
- **Total Lines of Code**: 170,246
- **API Issues**: 39 behavioral changes (low impact)
- **Estimated Changes**: 39+ lines of code
- **Overall Difficulty**: 🟢 Low

---

## Implementation Timeline

### Phase 0: Prerequisites Verification
- Verify .NET 10 SDK installation
- Check global.json compatibility (if present)
- Ensure clean working tree

### Phase 1: Atomic Upgrade
**Operations** (performed as single coordinated batch):
1. Update all project TargetFramework properties to net10.0
2. Update all package references to .NET 10-compatible versions
3. Remove System.Text.RegularExpressions package (included in framework)
4. Restore dependencies
5. Build entire solution
6. Fix any compilation errors

**Deliverables**: Solution builds with 0 errors

### Phase 2: Test Validation
**Operations**:
- Execute BTCPayServer.Tests test project
- Address any test failures
- Verify behavioral changes don't break functionality

**Deliverables**: All tests pass

### Phase 3: Final Verification
**Operations**:
- Run full solution build
- Verify no warnings introduced
- Confirm all packages resolved correctly
- Document any deviations or issues

**Deliverables**: Clean, working .NET 10 solution

---

## Dependency Analysis

### Project Dependency Order (Topological)

Projects are listed in dependency order (leaf nodes first):

1. **BTCPayServer.Client** (netstandard2.1) → net10.0
2. **BTCPayServer.Abstractions** (net8.0) → net10.0
3. **BTCPayServer.Common** (net8.0) → net10.0
4. **BTCPayServer.Rating** (net8.0) → net10.0
5. **BTCPayServer.Data** (net8.0) → net10.0
6. **BTCPayServer** (net8.0) → net10.0 (Main application)
7. **BTCPayServer.PluginPacker** (net8.0) → net10.0 (Tool)
8. **BTCPayServer.Tests** (net8.0) → net10.0 (Test project)

### Dependency Relationships

```
BTCPayServer.Client (no dependencies)
    ↓
BTCPayServer.Abstractions
    ↓
BTCPayServer.Common
    ↓
BTCPayServer.Data
    ↓
BTCPayServer (Main App)
    ↓
BTCPayServer.Tests
```

**Note**: BTCPayServer.Rating has dependencies on BTCPayServer.Client. BTCPayServer.PluginPacker depends on BTCPayServer.Abstractions.

---

## Project-by-Project Upgrade Specifications

### All Projects - Target Framework Update

Update `TargetFramework` element in each project file:

| Project | Current | Target | Location |
|---------|---------|--------|----------|
| BTCPayServer.Client | netstandard2.1 | net10.0 | BTCPayServer.Client/BTCPayServer.Client.csproj |
| BTCPayServer.Abstractions | net8.0 | net10.0 | BTCPayServer.Abstractions/BTCPayServer.Abstractions.csproj |
| BTCPayServer.Common | net8.0 | net10.0 | BTCPayServer.Common/BTCPayServer.Common.csproj |
| BTCPayServer.Rating | net8.0 | net10.0 | BTCPayServer.Rating/BTCPayServer.Rating.csproj |
| BTCPayServer.Data | net8.0 | net10.0 | BTCPayServer.Data/BTCPayServer.Data.csproj |
| BTCPayServer | net8.0 | net10.0 | BTCPayServer/BTCPayServer.csproj |
| BTCPayServer.PluginPacker | net8.0 | net10.0 | BTCPayServer.PluginPacker/BTCPayServer.PluginPacker.csproj |
| BTCPayServer.Tests | net8.0 | net10.0 | BTCPayServer.Tests/BTCPayServer.Tests.csproj |

**Special Note for BTCPayServer.Client**: Currently targets netstandard2.1. Upgrading to net10.0 for consistency and to leverage .NET 10 features.

---

## Package Update Reference

### Packages Requiring Upgrade

| Package | Current Version | Target Version | Affected Projects | Priority |
|---------|----------------|----------------|-------------------|----------|
| Microsoft.EntityFrameworkCore | 8.0.11 | 10.0.3 | BTCPayServer.Abstractions | High |
| Microsoft.EntityFrameworkCore.Design | 8.0.11 | 10.0.3 | BTCPayServer.Data | High |
| Microsoft.AspNetCore.Identity.EntityFrameworkCore | 8.0.11 | 10.0.3 | BTCPayServer.Data | High |
| Microsoft.AspNetCore.Mvc.NewtonsoftJson | 8.0.11 | 10.0.3 | BTCPayServer | High |
| Microsoft.AspNetCore.SignalR.Protocols.NewtonsoftJson | 8.0.11 | 10.0.3 | BTCPayServer | High |
| Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation | 8.0.11 | 10.0.3 | BTCPayServer, BTCPayServer.Tests | High |
| System.IO.Pipelines | 8.0.0 | 10.0.3 | BTCPayServer | Medium |
| System.Text.Json | 8.0.5 | 10.0.3 | BTCPayServer.Rating | Medium |
| Newtonsoft.Json | 13.0.3 | 13.0.4 | BTCPayServer.Client, BTCPayServer, BTCPayServer.Rating | Low |

### Package to Remove

| Package | Current Version | Reason | Affected Projects |
|---------|----------------|--------|-------------------|
| System.Text.RegularExpressions | 4.3.1 | Included with .NET 10 framework | BTCPayServer |

### Compatible Packages (No Update Required)

42 packages are already compatible with .NET 10.0 and require no changes:
- BIP78.Sender (0.2.5)
- BTCPayServer.Hwi (2.0.6)
- BTCPayServer.Lightning.All (1.6.13)
- CsvHelper (32.0.3)
- Dapper (2.1.35)
- And 37 more...

---

## Breaking Changes Catalog

### Known .NET 10 Breaking Changes

#### 1. System.Uri Behavioral Changes (32 occurrences)
**Impact**: Low - Behavioral changes in URI parsing and normalization
**Location**: BTCPayServer.Client
**Action Required**: 
- Test URI handling thoroughly
- Review URI construction and parsing logic
- Verify no breaking changes in client code

**Files Affected** (from assessment):
- BTCPayServer.Client project files

#### 2. System.Net.Http.HttpContent Behavioral Changes (7 occurrences)
**Impact**: Low - Changes in HttpContent behavior
**Location**: BTCPayServer.Client
**Action Required**:
- Test HTTP client operations
- Verify content serialization/deserialization
- Check HttpClient patterns

### Framework Migration Considerations

**Moving from netstandard2.1 to net10.0**:
- BTCPayServer.Client will gain access to full .NET 10 APIs
- May expose APIs not available in netstandard2.1
- Consider implications for library consumers (if any external consumers exist)

---

## Testing Strategy

### Multi-Level Testing Required

#### Build Validation
After upgrading all projects:
- [ ] Complete solution builds without errors
- [ ] Complete solution builds without warnings
- [ ] All NuGet packages restore successfully
- [ ] No package dependency conflicts

#### Unit Testing
- [ ] Run all tests in BTCPayServer.Tests project
- [ ] All tests pass
- [ ] No new test failures introduced
- [ ] Test execution time comparable to .NET 8

#### Integration Testing (if applicable)
- [ ] Verify application startup
- [ ] Test key workflows
- [ ] Validate API endpoints
- [ ] Check database connectivity

#### Behavioral Validation
Specifically test areas affected by API changes:
- [ ] URI construction and parsing (System.Uri changes)
- [ ] HTTP client operations (HttpContent changes)
- [ ] JSON serialization/deserialization
- [ ] Entity Framework operations

---

## Risk Assessment and Mitigation

### Risk Levels by Project

| Project | Risk Level | Risk Factors | Mitigation |
|---------|-----------|--------------|------------|
| BTCPayServer.Client | 🟡 Medium | 39 API behavioral changes, netstandard→net10.0 | Thorough testing, review URI/HTTP code |
| BTCPayServer.Abstractions | 🟢 Low | Simple package upgrade | Standard testing |
| BTCPayServer.Common | 🟢 Low | No package changes | Build verification |
| BTCPayServer.Rating | 🟢 Low | Minor package upgrades | Standard testing |
| BTCPayServer.Data | 🟢 Low | EF Core upgrade | Test migrations, DB operations |
| BTCPayServer | 🟢 Low | Multiple package upgrades | Comprehensive testing |
| BTCPayServer.PluginPacker | 🟢 Low | No package changes | Build verification |
| BTCPayServer.Tests | 🟢 Low | One package upgrade | Test execution |

### Specific Risks and Mitigations

#### Risk 1: Entity Framework Core Upgrade (8.0.11 → 10.0.3)
**Likelihood**: Low  
**Impact**: Medium  
**Mitigation**:
- Review EF Core 9.0 and 10.0 breaking changes documentation
- Test all database operations
- Verify migrations still work
- Check for deprecated API usage

#### Risk 2: API Behavioral Changes in BTCPayServer.Client
**Likelihood**: Medium  
**Impact**: Low-Medium  
**Mitigation**:
- Focus testing on URI and HTTP operations
- Review assessment-identified locations
- Test client library thoroughly
- Check for external library consumers

#### Risk 3: Package Dependency Conflicts
**Likelihood**: Low  
**Impact**: Medium  
**Mitigation**:
- Restore packages after all updates
- Resolve any version conflicts immediately
- Use consistent package versions across projects

---

## Detailed Execution Steps

### Step 1: Prerequisites Verification

**Actions**:
1. Verify .NET 10 SDK is installed (`dotnet --list-sdks`)
2. Check for global.json and update if necessary
3. Ensure working tree is clean (`git status`)
4. Create backup branch if not already on upgrade branch

**Success Criteria**:
- .NET 10 SDK available
- Clean working tree
- On correct branch

### Step 2: Update Project Files

**Actions**:
Update TargetFramework in all project files simultaneously:

```xml
<!-- Change from: -->
<TargetFramework>net8.0</TargetFramework>
<!-- or -->
<TargetFramework>netstandard2.1</TargetFramework>

<!-- To: -->
<TargetFramework>net10.0</TargetFramework>
```

**Projects to Update** (in any order, as this is atomic):
1. BTCPayServer.Client/BTCPayServer.Client.csproj
2. BTCPayServer.Abstractions/BTCPayServer.Abstractions.csproj
3. BTCPayServer.Common/BTCPayServer.Common.csproj
4. BTCPayServer.Rating/BTCPayServer.Rating.csproj
5. BTCPayServer.Data/BTCPayServer.Data.csproj
6. BTCPayServer/BTCPayServer.csproj
7. BTCPayServer.PluginPacker/BTCPayServer.PluginPacker.csproj
8. BTCPayServer.Tests/BTCPayServer.Tests.csproj

**Success Criteria**:
- All 8 project files show net10.0 as TargetFramework

### Step 3: Update Package References

**Actions**:
Update PackageReference versions in project files. See §Package Update Reference for complete matrix.

**Entity Framework Core Packages** (8.0.11 → 10.0.3):
- Update in BTCPayServer.Abstractions/BTCPayServer.Abstractions.csproj:
  - Microsoft.EntityFrameworkCore
- Update in BTCPayServer.Data/BTCPayServer.Data.csproj:
  - Microsoft.EntityFrameworkCore.Design
  - Microsoft.AspNetCore.Identity.EntityFrameworkCore

**ASP.NET Core Packages** (8.0.11 → 10.0.3):
- Update in BTCPayServer/BTCPayServer.csproj:
  - Microsoft.AspNetCore.Mvc.NewtonsoftJson
  - Microsoft.AspNetCore.SignalR.Protocols.NewtonsoftJson
- Update in both BTCPayServer/BTCPayServer.csproj and BTCPayServer.Tests/BTCPayServer.Tests.csproj:
  - Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation

**System Packages**:
- Update System.IO.Pipelines (8.0.0 → 10.0.3) in BTCPayServer/BTCPayServer.csproj
- Update System.Text.Json (8.0.5 → 10.0.3) in BTCPayServer.Rating/BTCPayServer.Rating.csproj

**Third-Party Packages**:
- Update Newtonsoft.Json (13.0.3 → 13.0.4) in:
  - BTCPayServer.Client/BTCPayServer.Client.csproj
  - BTCPayServer/BTCPayServer.csproj
  - BTCPayServer.Rating/BTCPayServer.Rating.csproj

**Remove Package**:
- Remove System.Text.RegularExpressions from BTCPayServer/BTCPayServer.csproj (functionality included in .NET 10)

**Success Criteria**:
- All 9 packages updated to specified versions
- System.Text.RegularExpressions removed
- No version conflicts

### Step 4: Restore Dependencies

**Actions**:
```bash
dotnet restore btcpayserver.sln
```

**Success Criteria**:
- Restore completes without errors
- All packages downloaded successfully
- No version conflict warnings

### Step 5: Build Solution

**Actions**:
```bash
dotnet build btcpayserver.sln --configuration Release
```

**Expected Outcome**:
- Solution builds successfully
- 0 errors
- Monitor warnings (some new warnings may appear)

**If Build Fails**:
- Analyze error messages
- Fix compilation errors related to API changes
- Most likely candidates:
  - URI usage in BTCPayServer.Client
  - HttpContent usage in BTCPayServer.Client
  - Any obsolete API usage

**Success Criteria**:
- Build succeeds with 0 errors
- Warning count acceptable (document any new warnings)

### Step 6: Run Tests

**Actions**:
```bash
dotnet test BTCPayServer.Tests/BTCPayServer.Tests.csproj
```

**Success Criteria**:
- All tests pass
- No new test failures
- Test execution time comparable to .NET 8

### Step 7: Final Verification

**Actions**:
1. Run full solution build again
2. Verify no package conflicts
3. Check for any remaining issues
4. Document any deviations from plan

**Success Criteria**:
- Complete solution builds cleanly
- All tests pass
- No unresolved issues

---

## Source Control

### Commit Strategy

**Recommended**: Single commit approach for atomic upgrade

```
git add .
git commit -m "Upgrade solution to .NET 10.0

- Updated all 8 projects from net8.0/netstandard2.1 to net10.0
- Upgraded 9 NuGet packages to .NET 10-compatible versions
- Removed System.Text.RegularExpressions (included in framework)
- Verified build and tests pass"
```

**Alternative**: If issues arise, use multiple commits:
1. Update target frameworks
2. Update packages
3. Fix compilation errors
4. Address test failures

### Branch Management

- Current branch: `copilot/upgrade-btcpayserver-sln-to-net10`
- Keep upgrade work isolated on this branch
- PR and review before merging to main

---

## Success Criteria

The upgrade is complete when:

1. **Technical Criteria**
   - ✅ All 8 projects target net10.0
   - ✅ All 9 packages upgraded to specified versions
   - ✅ System.Text.RegularExpressions removed
   - ✅ Solution builds with 0 errors
   - ✅ All tests pass
   - ✅ No package dependency conflicts

2. **Quality Criteria**
   - ✅ No new warnings introduced (or documented if unavoidable)
   - ✅ All behavioral changes tested
   - ✅ Code review completed
   - ✅ Documentation updated (if necessary)

3. **Validation Criteria**
   - ✅ Application starts successfully
   - ✅ Key workflows function correctly
   - ✅ Database operations work
   - ✅ API endpoints respond correctly

---

## Rollback Plan

If critical issues arise during or after upgrade:

1. **Immediate Rollback**:
   ```bash
   git reset --hard HEAD~1  # or to specific commit before upgrade
   git push --force-with-lease
   ```

2. **Partial Rollback**:
   - Revert specific package updates
   - Roll back individual project files
   - Keep working changes, revert problematic ones

3. **Investigation and Retry**:
   - Document specific failure
   - Research resolution
   - Apply fix
   - Retry upgrade

---

## Additional Considerations

### Performance

- Monitor application performance after upgrade
- .NET 10 includes performance improvements
- Benchmark critical paths if concerned

### Third-Party Dependencies

- All third-party packages assessed as compatible
- No known blockers from external dependencies
- NBitcoin, BTCPayServer.Lightning.* packages compatible

### Future Maintenance

- .NET 10 is Long Term Support (LTS)
- Support until November 2028
- Plan for .NET 12 LTS in 2028

---

## Appendix

### Package Version Matrix

See assessment.md "Aggregate NuGet packages details" section for complete package inventory.

### API Changes Details

See assessment.md "Top API Migration Challenges" section for detailed API compatibility analysis.

### Dependency Graph

See assessment.md "Projects Relationship Graph" section for visual dependency representation.

---

*This plan is ready for execution in the Execution stage.*
