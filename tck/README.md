# ReactiveSocket TCK/RI

This directory contains (the start of) a reference implementation and
TCK for the reactive sockets protocol implemented in Quark.

The Quark language was created to aid in the implementation of
protocols that require complex behaviors. The Quark language does not
have a native runtime; it exclusively transpiles into other high level
languages (currently Java, Python, JavaScript, and Ruby). For more about
Quark see: https://github.com/datawire/quark.

Please note that Quark is a new project under active development, so
if you have any trouble following the instructions here, please join
the public Slack channel by clicking on the Slack badge found at the
above link.

This directory contains the following files:

 - rxlib.q -- the core of the reference implementation
   + rxframe.q -- the frame definitions
   + rxws.q -- the web sockets glue
 - rxtest.q -- a test suite for rxlib.q
 - rxserver.q -- a TCK server
 - rxclient.q -- a TCK client

Because Quark does not have its own runtime you need to choose a
language to use in order to run quark compiled code. This README will
assume Python, however Java, Ruby, and JavaScript should work
similarly if all the necessary dependencies are available.

Use the following command to install the Quark compiler:

    # curl -sL https://raw.githubusercontent.com/datawire/quark/master/install.sh | sh
    ...

You can then run `quark --version` to verify that it worked:

    # quark --version
    Quark 0.6.40

You can now use the compiler to build the test suite, client, and server:

    # quark compile rxtest.q rxclient.q rxserver.q
    ...

By default, the compiler will produce source code in the `output`
directory. The output of the compiler is a set of interdependent
packages that use the native package tooling of the given backend.

    # tree -dL 2 output
    output
    ├── java
    │   ├── quark
    │   ├── rxclient
    │   ├── rxlib
    │   ├── rxserver
    │   └── rxtest
    ├── js
    │   ├── quark
    │   ├── rxclient
    │   ├── rxlib
    │   ├── rxserver
    │   └── rxtest
    ├── py
    │   ├── quark
    │   ├── rxclient
    │   ├── rxlib
    │   ├── rxserver
    │   └── rxtest
    └── rb
        ├── quark
        ├── rxclient
        ├── rxlib
        ├── rxserver
        └── rxtest

    24 directories

The quark compile command is useful if you want to view or distribute
the generated source, but otherwise it isn't usually necessary if you
want to *use* quark code. You can just use the quark install command
``quark install <url-or-file>.q --<lang>``, and that will detect any
changes in your .q file (or in any .q files that it depends on) and
perform an incremental rebuild/reinstall using the appropriate
toolchain for the language you select.

Note that you will need to have the appropriate tooling installed for
your language. The quark install command *should* check and give you a
useful error message if any necessary tools are missing.

The command below illustrates how this works for python, but other
languages work similarly:

    # quark install rxtest.q rxclient.q rxserver.q --python
    ...

# Running the tests

The details of running Quark generated code differ depending on the
backend in use, however the Quark compiler can run the code for
you. Please note that you need to run ``quark install
<url-or-file>.q`` first in order for the quark run command to work:

    # quark run rxtest.q --python
    ...
    Running: rxtest
    =============================== starting tests ===============================
    rxtest.RoundTripTest.testSetup1 [5 checks, 0 failures]
    rxtest.RoundTripTest.testSetup2 [5 checks, 0 failures]
    rxtest.RoundTripTest.testLease1 [2 checks, 0 failures]
    rxtest.RoundTripTest.testLease2 [2 checks, 0 failures]
    rxtest.RoundTripTest.testLease3 [2 checks, 0 failures]
    rxtest.RoundTripTest.testLease4 [2 checks, 0 failures]
    =============================== stopping tests ===============================
    Total: 6, Filtered: 0, Passed: 6, Failed: 0


# Running the server

    # quark run rxserver.q ws://localhost:8910 --python

# Running the client

    # quark run rxclient.q ws://localhost:8910 --python

# How to use this

If you are building your own reactive sockets implementation, you can
use this TCK/RI in multiple ways:

 - You can use some or all of the reference implementation to
   bootstrap your own implementation
 - If you are building a client implementation you can validate it
   against the provided server.
 - If you are building a server implementation you can validate it
   using the provided client.

# Status

The implementation is currently mostly skeletal; however it does
include a functioning server and client that do simple
request/responses and rate limited request/responses using the
reactive sockets lease mechanism.
