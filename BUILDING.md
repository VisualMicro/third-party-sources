# Upstream build information

Extract each component's source archives and follow its upstream build instructions. Additional compilers, dependencies and platform prerequisites may be required.

- GDB: extract avr-gdb-atmel3.6.1.tar.bz2 and the pinned Arduino toolchain-avr ZIP. Read Readme.md, build.conf and gdb.build.bash in the builder archive and gdb/README in the source archive. The debugger version is 7.8; 7.3.0 is the GCC/toolchain identity. The builder's additional GDB patch application is commented out. VERSION.json records the source mirror and builder revision. The Arduino build scripts are supplied; the historical generic Atmel wrapper named by SOURCES.README is not included.
- ctags: extract ctags-5.8-arduino11.tar.xz and follow its README and configure/build inputs. The published source checksum sidecar is included. This is Arduino's Exuberant Ctags fork.
- ZedGraph: extract the pinned v5.1.7 ZIP. Its project uses MSBuildVersioning to generate VersionInfo.Designer.cs from 5.1.7.$REVNUM$. Revision stamping depends on the repository/build context, so an extracted ZIP does not by itself select assembly revision 430. Preserve the upstream strong-name build inputs where required.
- Ude: extract ude-d17ab728-library-and-build-sources.zip. Use the src/Library project and upstream solution/build inputs. The library assembly version is 0.1.0.0; its package version is UDE.CSharp 1.1.0. Tests requiring the omitted fixtures need separately supplied data.

The source archives contain upstream ancillary files and dependencies with their own notices. Preserve those notices when using or redistributing the sources.
