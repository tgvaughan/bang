Bang!
=====

A very simple command line utility for evaluating snippets of
code embedded in text files.

While the general problem of mixing text and code has been addressed
many times over the years in very sophisticated ways (see literate
programming, macro languages), I haven't yet found any sufficiently
clean solution to the following problem.

Suppose we have a program `prog` which takes a single file
`config.txt` as input.  We run it as follows:

    $ prog config.txt
    
Bang! allows two things: for code from an arbitrary number of
different scripting languages to be embedded in the configuration
and for this code to be replaced with its output using the
following simple command:

    $ prog `bang config.txt`
    
Additionally, you can include variable substitution using the
following syntax:

    $ prog `bang -d var="Some value" config.txt`

Thus, Bang! allows us to programmatically construct parts of config
files, and to use command-line options with programs that only take
a config file.

Installation
------------

To install Bang!, copy it into a directory in your $PATH and ensure
that the execute bit is set.

Ensure you have Python 3 installed, and that `python3` is aliased
to it.


Usage
-----

The usage instructions are as follows:

    usage: bang [-h] [-o OUT] [-d def] file

    Bang! Adding Turing Completeness to your config files since 2020.

    positional arguments:
    file                  name of file to process

    optional arguments:
    -h, --help            show this help message and exit
    -o OUT, --out OUT     output file
    -d def, --define def  define variable using syntax k=v

By default, the generated output is written to a temporary file, the
name of which is written to standard output.  This permits the use of
a backtick evaluation of Bang! wherever a filename is expected.

(Of course, this means that the result of the evaluation will remain
in your system's temporary storage until this storage is cleaned.)

Alternatively, you can directly specify an output file using `--out`.
The output file name `-` directs Bang! to write directly to standard
out: this is the only instance in which the file name is not printed.


## Code blocks

Code blocks are specified using the following notation in the input file:

    !!![VAR=]COMMAND
    BODY (line 1)
    BODY (line 2)
    ...!!!
    
Here COMMAND is any command that takes a single file as argument.  The
body of the code block is written to a temporary file, then the
command is executed with the name of that file as its final argument.
The code block is then replaced with whatever that command writes to
standard output.

There is no requirement that the block start at the beginning of a line,
nor that it end at the end of a line.

If the command is prefixed by `VAR=` where VAR is some (far too
liberal) sequence of characters, the result of the evaluation will
also be saved to a variable with that name.

Here is an example of bash code block:

    !!!bash
    for i in `seq 1 5`; do
        echo "Hi $i"
    done
    !!!

And here is an example of an R code block:

    !!!R --slave -f
    cat(lgamma(20))!!!

Here's an example of the same R code block that also saves the result
to a variable:

    !!!VAR = guile -s
    (display (reverse '(cow sheep pig)))!!!

Any occurrence of "VAR" from this point on in the input file will be replaced
by `(pig sheep cow)`.

Finally, for including the output of commands which do not require an input
file, you can skip the body entirely.  For instance, `!!!uptime!!!` works.
So does `!!!dc -e "5 k 2 v p"!!!`.


## Variables

Bang!'s variable system is so simplistic that you might argue that it's misleadingly
named.  Basically a search and replace is performed on every fragment of the input --
both code (pre-evaluation) and non-code -- so that **any** occurrence of the "variable"
name will be replaced with the corresponding value.

I've done things this way to give you the flexibility to avoid any syntactical conflicts
with exiting variable naming schemes.  (E.g. if Bang! enforced "$var" notation, suddenly
we'd need to escape the "$" in shell variables.)  This way it's up to **you** to avoid
namespace collisions.

(This is inspired by the flexibility M4 allows with respect to macro naming.)


License
-------

Although it seems ridiculous to explicitly license a 80-line python script,
here goes.

Bang! is free as in freedom, and is released under the conditions of the 
version 3 of the GNU General Public License.  A copy of this license is
found in this directory in the file named COPYING.
