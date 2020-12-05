# book-maker

A tool for creating a Polyglot .bin opening book using lc0.
The gist is that it simply runs lc0 in `go infinite` mode, periodically examines the tree, and writes an entry into the opening book for any position that has a sufficient number of visits to be considered sufficiently converged.

Fork of: repo [here](https://github.com/petersn/lczero-book-maker) updated for new Lc0 engine backend.

## Usage

You must first apply a patch to lc0 that adds a UCI command `dumpnode` that book-maker.py uses to probe the tree.
To do this, checkout the `dumpnode` branch of [my fork of leela-chess](https://github.com/bharadwaj6/lc0), and build lc0.

One you have that compiled you can invoke book-maker.py.
The options are relatively obvious:

```
usage: book-maker.py [-h] --command CMD --output PATH --visit-threshold INT
                     [--dump-interval SECONDS] [--print-tree]

optional arguments:
  -h, --help            show this help message and exit
  --command CMD         Command to run lc0. (Split via shlex.split, and
                        executed.)
  --output PATH         Output path to dump a .bin to.
  --visit-threshold INT
                        Only write moves into the book if their parent has at
                        least this many visits.
  --dump-interval SECONDS
                        Dump a .bin every this many seconds.
  --print-tree          Print the book tree upon each probing. (May print lots
                        of text.)
```

An example usage:

```
$ ./book-maker.py --command "/path/to/leela-chess/lc0/build/lc0 -w /tmp/some-network-file --cpuct=100.0" --output opening-book.bin --dump-interval=60 --visit-threshold=100000
```

This will launch lc0 with the given weights file and Cpuct value, and every minute write a book to opening-book.bin containing the most visited move of every position with at least 100k visits.
By default lc0 tends towards exploring extremely deeply, and not very broadly, so here we increased the Cpuct to get a broader book with fewer stupidly long lines.

TODO: Evaluate how much increasing the Cpuct changes the moves selected at extremely high visit counts.

## License

Because this code uses `python-chess`, it is required to be released under the terms of the GPL3.
However, I [*additionally*](https://opensource.stackexchange.com/questions/4137/can-public-domain-use-gpl-licensed-library-program) release the code into the public domain, and under CC0 (for users outside of the US) as well.
