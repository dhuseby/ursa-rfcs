- Feature Name: ursa-config
- Start Date: 2019-01-10
- RFC PR:
- Ursa Issue:
- Version: 1

# Summary
[summary]: #summary

This RFC covers the configuration management system for the Hyperledger Ursa
crypto library. The configuration system is key to achieving the level of
flexibility necessary to make Hyperledger blockchain platform and tools capable
of meeting the needs of the broadest set of use cases.

# Motivation
[motivation]: #motivation

The stated goal of Ursa is to provide an umbrella library that contains many
different implementations of basic cryptographic primitives (e.g. hash
algorithms, encryption algorithms, digital signatures, etc) so that clients of
the library can pick and choose which primitives should be included in the
version of Ursa their project relies upon. Hyperledger Ursa also contains
new research and experimetal implementations of cryptographic primitives, such
as zero-knowledge proofs, that can also be included in a build of Ursa. The
configuration system is the method by which clients can specify what primitives
are and are not included in a specific build of Ursa.

The complex nature of an Ursa build configuration makes it a good idea to have
a tool that walks users through the process of generating a build configuration
for their custom build. This tool can also be used to maintain the pre-defined
build configurations that are maintained by the Ursa developers. The rest of
this document outlines the design for such a tool.

# Guide-level explanation
[guide-level-explanation]: #guide-level-explanation

The build configuration tool for Hyperledger Ursa is designed to be generic
and not specific to the Hyperledger Ursa project. It is implemented as a [custom Cargo
sub-command](https://doc.rust-lang.org/cargo/reference/external-tools.html?highlight=subcommand#custom-subcommands)
so that it isn't tied to a specific project. Custom Cargo sub-commands are
installed locally using the `cargo install` command and they can be executed
like the other Cargo commands. If we call our command `cargo-foo`, once it is
installed, it can be executed by issuing the command `cargo foo` in your
terminal.

The proposed name for the Ursa build command is `cargo-cfg`. The name is meant
to be short and there is already a `cargo-config` that has nothing to do with
configuring a Cargo-managed Rust project. The `cargo-cfg` command reads in a
file defining the available configuration options and presents a nice,
menu-driven interface to the user to make their selections. It is also able to
load existing configurations to pre-populate choices and save configurations to
disk for use with the [Ursa build command
`cargo-texo`](https://github.com/hyperledger/ursa-rfcs/0000-ursa-build.md).

# Reference-level explanation
[reference-level-explanation]: #reference-level-explanation

The `cargo-cfg` tool is inspired by [the configuration tool used by the Linux
Kernel project](https://en.wikipedia.org/wiki/Menuconfig). However it uses
[TOML](https://github.com/toml-lang/toml) to define the questions and options
presented to the user instead of the Kconfig language used in the Linux Kernel
configuration tool. The tool presents the user an
[ncurses](https://en.wikipedia.org/wiki/Ncurses) console interface for
selecting the features and primitives to include in an Ursa build. Once the
user has completed their configuration, the tool saves the configuration file
for the build step to process while building Ursa.

# Drawbacks
[drawbacks]: #drawbacks

The only real drawback to using this design is that the configuration tool is
a custom Cargo sub-command that requires being installed separately.

# Rationale and alternatives
[alternatives]: #alternatives

- Why is this design the best in the space of possible designs?
- What other designs have been considered and what is the rationale for not
  choosing them?
- What is the impact of not doing this?
- For incorporating new protocol implementations what other implementations
  exist and why were they not selected?
- For new protocols, what related protocols exist and why do the not satisfy
  requirements?

# Prior art
[prior-art]: #prior-art

The configuration step in Ursa is complicated with many different
implementations, features, primitives, and other settings that must be defined
to build Ursa correctly for a given application. The Linux Kernel project has
simimlar requirements for configuring a build of the Linux Kernel and it is
the primary inspiration for this design. 

The Linux Kernel configuration tool is very good at structuring all of the
options logically and in a way that makes it easy to understand exactly what is
and is not included. The main drawback to the Linux Kernel configuration tool
is that sometimes it isn't obvious what the interdependencies are between
different configuration options. It often makes options unavailable without
telling the user exactly what has to be enabled to make them available.

![Linux Kernel Menuconfig](0000-ursa-build/menuconfig.gif "Linux Kernel Menuconfig")

# Unresolved questions
[unresolved]: #unresolved-questions

- What parts of the design do you expect to resolve through the RFC process
  before this gets merged?
- What parts of the design do you expect to resolve through the implementation
  of this feature before stabilization?
- What related issues do you consider out of scope for this RFC that could be
  addressed in the future independently of the solution that comes out of this
  RFC?
