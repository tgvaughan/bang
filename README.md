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

    $ prog `bang -d var1="Some value" config.txt`

