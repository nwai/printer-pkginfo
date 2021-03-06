printer-pkginfo
===============

Creates a nopkg-style pkginfo file from a plist file to install printers with
[Munki](https://github.com/munki/munki).

`printer-pkginfo` can be run interactively or using an XML plist file as
input. In interactive mode, users are guided through filling in the basic
information for the printer, but more advanced options used by `lpadmin`
are not available (with the exception of duplexing).

```
usage: printer-pkginfo [-h] [-p PLIST] [-i] [-o OUTFILE] [-c CATALOG]

A python script to create nopkg-style files for installing printers onto
client systems using Munki (https://github.com/munki/munki). Printers can be
imported from a pre-existing XML plist file or can be created interactively.
Unless a file path is specified with -o, the pkg file will be written to
STDOUT.

optional arguments:
  -h, --help            show this help message and exit
  -p PLIST, --plist PLIST
                        Path to an XML plist file containing key/value pairs
                        for version, display_name, queue_name, ppd, location,
                        and other options. See example-printer.plist for an
                        example.
  -i, --interactive     Create pkginfo file in interactive mode rather than
                        with a .plist file
  -o OUTFILE, --outfile OUTFILE
                        Write pkginfo to plist file rather than STDOUT
  -c CATALOG, --catalog CATALOG
                        Set Munki catalog (default without -c is "testing")
  -v VERSION, --version VERSION
                        Set package version if creating an updated pkginfo
                        file for an existing printer (default without -v is
                        0.1)
  --prefix PREFIX       With -i, set prefix for printer name as it appears in
                        Munki manifests


To import printers into Munki, copy the output file to the pkgsinfo/ directory
of your Munki repo and then re-run makecatalogs
```

By default, pkginfo files are written to the STDOUT for easy inspection. This
behavior can be changed by running the command with the `-o` option or using
output redirection.

### Non-Interactive Usage

After cloning the repo to your local system (this doesn't have to be the same
system that you're running Munki on, but if it's not, that adds an extra step),
copy the `example-printer.plist` file and edit the options to fit your own
printer setup.

Note that the `requires` string must match the name of the driver package
installed on the target systems. If the drivers are unavailable from your
Munki server and aren't found on the target system, the install will fail.

After you've edited the plist file for your printer, run `printer-pkginfo`

```
./printer-pkginfo -p example-printer.plist -o example_pkginfo-1.0.plist
```

Move the resulting plist file to your `pkgsinfo/` directory on your Munki
server, then rebuild the catalogs using `makecatalogs`. You can now add the
printer to manifests using `manifestutil`, where the `name` key from your
originating plist file corresponds to the package name in Munki.
