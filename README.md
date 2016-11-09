phpCallGraph
============

A tool to generate static call graphs for PHP source code. The graphs can be leveraged to gain a better understanding of large software systems or even to debunk design flaws in them.

    http://phpcallgraph.sourceforge.net

This program is free software; you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation; either version 3 of the License, or (at your option) any later version.

This program is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details.

You should have received a copy of the GNU General Public License along with this program.  If not, see <http://www.gnu.org/licenses/>.

Usage
-----

- Go to folder bin and run phpcallgraph.
- The generator requires at least PHP 5.2 (and tested also with PHP 7.0).
- In order to generate output formats other that 'txt' or 'cga' the Graphviz toolkit which can be downloaded at http://www.graphviz.org is required.
- For the 'umlgraph' output format you need pic2plot, which is part of the GNU plotutils package.

> bin/phpcallgraph [-f <string>] [-o <string>] [-r] [-d <string>] [-n] [-p] [-a <string>] [-v] [-g] [-h] [--] <string:sources> [<string:sources> ...]
> 
> Arguments:
>     <string:sources>        Files and/or directories to analyze
> 
> Options:
>     -f / --format           Set output format. Can be 'txt', 'array', 'umlgraph'
>                             'deadcode', 'cga' or one of the formats supported
>                             by dot, e.g. png, svg, pdf, ps, ...
>                             (see http://graphviz.org/doc/info/output.html)
>     -o / --outputfile       Output file
>     -u / --umloutputdir     UML output directory (only used for the umlgraph output
>                             format
>     -r / --recursive        Analyze directories recursive
>     -d / --dotcommand       Set dot command
>     -i / --ignore           Set a regular expression for files or folders to ignore
>     -n / --noexternalcalls  Do not show calls to methods or functions which are
>                             external to a class
>     -p / --phpfunctions     Show calls to internal PHP functions
>     -a / --autoload         Sets a PHP file with an autoload function which will
>                             be included into the sandbox of the InstantSVC
>                             CodeAnalyzer
>     -v / --verbose          Verbose mode for text output format
>     -g / --debug            Print debug information
>                             (helpful if you get no output at all, since it
>                             shows errors during code analysis)
>     -h / --help             Display help

Bugs and improvement
--------------------

This program has bugs, please open [issues](https://github/Seb35/phpcallgraph/issues) or, better, [pull requests](https://github.com/Seb35/pulls).

I plan to improve the quality of phpCallGraph:
* fixing major bugs: autoloading (e.g. Composer autoloader), missing indices in arrays
* in the longer term, improve the inner quality:
  * I was surprised because ezReflection does not add a lot of features compared to the standard (today’s) Reflection and it is responsible of quite a lot of warnings/fatal errors,
  * perhaps use some phpDocumentor Reflection packages ([Reflection](https://github.com/phpDocumentor/Reflection), [TypeResolver](https://github.com/phpDocumentor/TypeResolver)) if they prove to be good quality,
* in parallel, given the huge graphs it creates for huge packages like MediaWiki, I had the idea of creating a visualisation with [D3.js](https://d3js.org) to dive into the call graph with various zoom levels, perhaps linking the call graph of a plugin/extension to a high-level graph of the underlying software, displaying only relevant parts of it (exceptions, router, global functions, hooks); just an idea to be better defined and improved.

Miscellaneae
------------

### 3D Graph Exploration

You can use the CGA framework available at http://cgs.hpi.uni-potsdam.de/trac/cga/ to explore the graph with elaborate 3D visualization techniques.

In order to generate input file for CGA set the format option to `cga', e.g.

> phpcallgraph -f cga -o testfiles.str test/testfiles/

### Analysis of code in the global scope

Code in the global scope (outside any functions or methods) can be analyzed with the help of a little workaround: Such code can be manually wrapped in a dummy function called dummyFunctionForFile\_filename\_php() which will then be recognized by phpCallGraph. Of course this is not very elegant but currently the only feasible way due to some conceptual restrictions resulting from the utilization of the InstantSVC CodeAnalyzer.

### Dead Code Detection

You can get a list of methods and functions which are never called by using the output format 'deadcode', e.g.

> bin/phpcallgraph -r -f deadcode test/testfiles/

Examples
--------

> bin/phpcallgraph -n -f png -o PHPCallGraph.png src/PHPCallGraph.php
> bin/phpcallgraph -f png -o phpcallgraph-library.png src/drivers/ src/PHPCallGraph.php
> bin/phpcallgraph -r -f png -o phpcallgraph-src.png src/
> bin/phpcallgraph -r -f png -o phpcallgraph.png src/ lib/
> bin/phpcallgraph -r -p test/testfiles/
> bin/phpcallgraph -p -- test/testfiles/Foo.php test/testfiles/Bar.php
> bin/phpcallgraph -r -f array test/testfiles/ | php -r '$a = unserialize(file_get_contents("php://stdin")); var_export($a);'
> bin/phpcallgraph -r -f deadcode test/testfiles/
> bin/phpcallgraph -g -r -i "PHPCallGraph.php|lib/ezcomponents" -f png -o phpcallgraph-src1.png src/ lib/

Authors
-------

- Falko Menge <fakko at users dot sourceforge dot net>
- Till Klampaeckel <till at php dot net>
- Christian Weiske <cweiske at php dot net>
- Martin Cleaver <mrjc at users dot sourceforge dot net> (UmlGraphSequenceDiagramDriver)
