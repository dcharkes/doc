# Seting up a Spoofax and StrategoXT development environment

A short tutorial on how to set up a development environment for Spoofax and StrategoXT.

## Spoofax

Follow these steps to set up a Spoofax development environment.

1. Install Eclipse. The recommended flavor and version is Eclipse Standard 4.3.2, which can be downloaded from http://eclipse.org/downloads/.
2. Install Spoofax nightly into Eclipse, see http://metaborg.org/wiki/spoofax/download.
3. Check out the Spoofax project from git: https://github.com/metaborg/spoofax.
4. Import the Spoofax runtime project, org.strategoxt.imp.runtime, into Eclipse.

You now have a local copy of the Spoofax runtime which is built my Eclipse automatically.
A new Eclipse instance needs to be started to use the local Spoofax runtime instead of the one that is installed into your Eclipse.

1. Make a copy of the Spoofax-IMP (with assertions) launch configuration and change it to your needs.
2. Debug (or run) the copied launch configuration, a new Eclipse instance will be started.

Now you can test your changes in the new Eclipse instance. 

The Spoofax runtime is the core of Spoofax, but there are many other projects that are included as part of Spoofax.
To work on these projects, follow the same procedure as with the runtime, check the project out and import it into Eclipse.
When a new instance is started, it will use the local project instead of the installed one.

## StrategoXT

StrategoXT contains the Stratego compiler, the Stratego standard libraries and some integration with Spoofax. To build StrategoXT, follow these steps.

1. Check out the java-boostrap branch of StrategoXT: https://github.com/metaborg/strategoxt/tree/java-bootstrap.
2. Change directory into strategoxt/strategoxt.
3. Follow the instructions in BUILDING.md to build StrategoXT.

If the build is sucessfull, a new strategoxt.jar can be found in <<FILL IN>>.
To test your changes in conjunction with Spoofax, import the org.strategoxt.strj project from strategoxt/strategoxt/stratego-libraries/java-backend into Eclipse and start a new instance.
