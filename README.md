# Apache mod_macro Simulator

This is a simple Perl script to approximate the expanded Apache configuration which will be generated by the Apache `mod_macro` module, which is [a built-in module in Apache 2.4 and later](http://httpd.apache.org/docs/current/mod/mod_macro.html) and [available as a third-party module for earlier versions](http://www.cri.ensmp.fr/~coelho/mod_macro/).

The original use case was to check that macros result in the same configuration after being introduced into existing files, or restructured. 

There is no guarantee that the output will be identical to that produced by the actual module, so caution should be taken if using this for debugging.

## Usage

Pass one or more Apache config files as command-line arguments or on standard input

```shell
perl parse_macros.pl /path/to/file.conf
perl parse_macros.pl /path/to/file.conf /path/to/another-file.conf
ssh someserver.example.com cat /remote/file.conf | perl parse_macros.pl
```

## Operation

The script actually parses the config in two phases:

1. The configuration is transformed by regex into a Perl script where every `<Macro>` block is a sub-routine, and every `Use` statement invokes the appropriate sub-routine.
2. The generated Perl is then run through `eval`, which prints the expanded Apache configuration to standard output.

## Limitations

The simulator currently has the following limitations

* Macro names must be valid suffixes to a Perl subroutine name, as Macros are currently declared as global subs.
* Macro definitions cannot contain `Use` statements, because the parsing is not recursive.
* `UndefMacro` is not supported.
* All files must be supplied on input, as there is no support for the `Include` directive.