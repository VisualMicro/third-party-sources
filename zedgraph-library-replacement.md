# Replacing ZedGraph in Visual Micro

Last reviewed: 2026-11-27

These instructions apply to installations using ZedGraph 5.1.7.430. Check the installed library's identity before preparing a replacement.

Visual Micro's graph visualizations use ZedGraph as a separate library. You can prepare an interface-compatible modified library for your own installation. Keep a backup of the installed files so you can restore them. Package updates or repair can replace your modified files.

## Obtain the source

Download the [published source bundle](https://github.com/VisualMicro/third-party-sources/releases/download/2026-11-26/VisualMicro-third-party-sources-2026-11-26.zip) from the [Visual Micro source release](https://github.com/VisualMicro/third-party-sources/releases/tag/2026-11-26). Its SHA-256 is:

```text
38C85340E95ECCDCF67477F823E47372AEBEF0C23DF9AF30ED61485D00F63EFD
```

Extract the nested `components/zedgraph-5.1.7/zedgraph-e7c73be838ea499de0a997a981aaae1caf0ed507.zip` into your own working directory, outside the installed extension. It is pinned to upstream tag `v5.1.7`, commit `e7c73be838ea499de0a997a981aaae1caf0ed507`. Read the bundle's `BUILDING.md`, component version record and licence files. Installations containing `5.1.15.35559` require source/package information for that version.

ZedGraph remains covered by LGPL 2.1 or later. Preserve its notices and licence, and identify your modifications. The library's replacement rights and source-distribution conditions are described in the [LGPL 2.1 text](https://www.gnu.org/licenses/old-licenses/lgpl-2.1.html), including section 6. This procedure does not require Visual Micro's proprietary source or private code-signing certificate.

## Preserve the assembly identity

The assembly identity for version 5.1.7.430 is:

```text
ZedGraph, Version=5.1.7.430, Culture=neutral, PublicKeyToken=02a83cbd123fcd60
```

Keep the assembly name, version, culture, public-key identity and the APIs used by the existing visualizations. Retaining only the filename is insufficient. These applications use .NET Framework assembly binding; renaming a DLL or giving it another strong-name key does not make it satisfy the original reference. A version redirect is not a general substitute for a different public-key identity. Do not change the host application's configuration or install a replacement into the GAC as part of this procedure. [Microsoft assembly-binding reference](https://learn.microsoft.com/en-us/dotnet/framework/deployment/how-the-runtime-locates-assemblies)

The upstream source includes `source/zedgraphkey.snk`. Its public-key token matches the identity above. Keep `SignAssembly=true` and `AssemblyOriginatorKeyFile=zedgraphkey.snk` in the library project. This upstream key is an assembly-identity build input, distinct from a trusted Windows publisher certificate. A Visual Micro Authenticode certificate is not needed to reproduce the CLR identity. [Microsoft strong-name reference](https://learn.microsoft.com/en-us/dotnet/framework/app-domains/strong-named-assemblies)

## Prepare and build your modified library

Use a compatible Windows .NET Framework/MSBuild development environment with the upstream project's .NET Framework 3.5 Client targeting support. Preserve the library's original framework compatibility.

1. Make your library changes in the extracted working copy. Preserve interfaces called by the installed visualizations and record your changes.
2. In that working copy's `source/ZedGraph.csproj`, disable the active `GitVersionFile` task in the `BeforeBuild` target. Remove its `MSBuildVersioning.1.0.34.0` targets import if that versioning dependency will no longer be used. Keep the standard C# targets, strong-name settings, source inputs and resources.
3. Set the compiled `source/Properties/VersionInfo.Designer.cs` to contain `using System.Reflection;` and exactly one `[assembly: AssemblyVersion("5.1.7.430")]` attribute. Update the noncompiled `VersionInfo.cs` template consistently so a later regeneration cannot silently introduce another identity. Do not add a duplicate assembly-version attribute elsewhere.
4. Build the `source/ZedGraph.csproj` library in Release/Any CPU. Supply `SolutionDir` as the extracted upstream root, including its trailing directory separator, if building the project directly: the project also imports `$(SolutionDir)\.nuget\nuget.targets`. Resolve any legacy build-tool prerequisite before proceeding. Do not substitute a DLL from an unrelated package or assume that upgrading the whole upstream solution preserves compatibility.
5. Inspect the produced DLL's actual identity and verify its strong name with the .NET Framework SDK `sn.exe -vf <replacement-path>`. A successful build alone does not establish the intended version or public-key token. Record the output hash and your build environment.

The checked-in generated version file in this archive contains `5.1.6.405`; the original template contains `5.1.7.$REVNUM$`. Neither is a reliable replacement identity without the explicit preparation above. Passing an SDK-style `AssemblyVersion` property alone does not override this project's compiled source attribute. The controlled version preserves binary binding; retain a separate record of your modified build so it is distinguishable from the original release.

If you change or rebuild localized resources, keep the corresponding `ZedGraph.resources.dll` files in their original culture subdirectories and verify their identities/signing as a consistent set. Do not overwrite localized files from an unrelated version. Unchanged shipped resource files can remain in place when your library change does not require new resources.

## Account for Windows signing

Strong-name signing and Authenticode are separate. A rebuilt or modified DLL cannot retain Visual Micro's original valid Authenticode signature. If your Windows application-control policy requires a trusted publisher, sign your finished replacement with a certificate accepted by that policy, using your own provider or administrator-approved signing arrangement. Sign after all code, resource and version changes. Verify the strong name again after Authenticode signing, and verify the Windows signature with the Windows SDK SignTool, for example `signtool verify /pa /v <replacement-path>`.

Smart App Control considers certificates from trusted providers; a local self-signed certificate is not equivalent to that trust. Signing with a trusted certificate also does not prove that every managed policy will allow the file. Do not disable Smart App Control or strong-name verification as a replacement step. If Windows blocks the replacement, retain the Code Integrity event details and resolve the trust requirement with your administrator or Visual Micro before treating the replacement arrangement as usable. [Microsoft Smart App Control signing requirements](https://learn.microsoft.com/en-us/windows/apps/develop/smart-app-control/code-signing-for-smart-app-control)

Changing the installed DLL does not produce an officially signed Visual Micro package. Repacking a VSIX changes its signed contents and invalidates the original container signature.

## Locate and replace the installed copies

Close every instance of the target host before copying files. Identify the actual Visual Micro extension installation root for that host and instance from its extension installation information or installer log. Do not use a build-output folder or assume that the newest matching folder belongs to the host you intend to change. AS7 has a different installation/package mapping; establish its root separately.

All paths below are relative to that installed extension root:

| Host family | ZedGraph locations to inspect |
|---|---|
| VS2019 | `Micro Extensions/Debugger Visualizations/MultiPlot/bin/ZedGraph.dll`, `AnalogGraphExample/bin/ZedGraph.dll` and `PerformanceMonitor/bin/ZedGraph.dll` under the same visualization parent |
| AS7 | Inspect its own installed extension for the applicable visualization paths and verify each library's identity before replacement |
| VS2022 and VS2026 | The same three visualization paths, plus `ZedGraph.dll` at the extension root where present |

Enumerate every `ZedGraph.dll` within the selected extension root and compare identities before replacement. Back up each original DLL, relevant resource files and hashes outside the extension directory, preserving relative paths. Copy the same compatible replacement into each matching ZedGraph location in that installation so the first visualization opened does not determine whether an old or modified copy loads. Repeat separately for each installed host instance you wish to change. Do not alter other extensions' private libraries.

You can read an assembly identity without executing its code using PowerShell:

```powershell
[Reflection.AssemblyName]::GetAssemblyName('C:\path\to\ZedGraph.dll').FullName
Get-FileHash -LiteralPath 'C:\path\to\ZedGraph.dll' -Algorithm SHA256
```

.NET Framework can reuse an assembly already loaded with the same identity and can resolve strong-named dependencies through binding policy or the GAC. Restart the host fully after replacement; closing only a graph window is insufficient. If the original still loads, inspect the loaded module path and binding diagnostics rather than guessing another copy or editing global binding settings.

## Check the replacement and restore if needed

After restarting, open MultiPlot, AnalogGraphExample and PerformanceMonitor where installed. Check the actual loaded ZedGraph path/version, a visible effect of your intended modification, graph updates, channel colour and tick settings, zoom/pan, localized text and repeated open/close. Compare host responsiveness with the original. Inspect the Visual Studio ActivityLog and Windows `CodeIntegrity/Operational` log when loading fails; use equivalent AS7 host diagnostics for that installation.

If a binding, trust or functional problem occurs, close the host, copy the saved original set back to its original paths and restart. Confirm original behavior returns. Keep your source/build record and diagnostic evidence for follow-up. Updating or repairing Visual Micro may restore the publisher's DLLs, so recheck the actual installed version before applying a replacement again.

Check each VS2019, AS7, VS2022 and VS2026 installation separately. Application-control requirements can differ between installations.
