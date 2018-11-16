# csv-compare

## Build status 
[![Build Status](https://travis-ci.org/modelica-tools/csv-compare.svg?branch=master)](https://travis-ci.org/modelica-tools/csv-compare) [![Build status](https://ci.appveyor.com/api/projects/status/wa6sd1cc7xotpf0f/branch/master?svg=true)](https://ci.appveyor.com/project/modelica-tools/csv-compare/branch/master)

## About

The CSV Result Compare can be used to compare curves from one CSV file with curves from other CSV files using a special algorithm.

The files are not compared 1:1 but a tube is generated around the curve and the values are checked to be inside the tube. The tube can be changed by setting a tolerance at the command line interface (`--tolerance`). The tolerance is a double value setting the width of the tube at discontinuity in x-direction.

## Documents

For build instructions see: [Build.md](https://github.com/modelica-tools/csv-compare/blob/master/BUILD.md) License information is provided in: [LICENSE](https://github.com/modelica-tools/csv-compare/blob/master/LICENSE)

## Usage

To see current usage information start the tool with the command line argument `--help` or `-h`.

You can set the arguments that way:
- `--argument=value`
- `--argument value`
- `-a=value`
- `-a value`

To check two CSV files (compare "file_compare.csv" with the reference file "file_base.csv" in verbose mode and save the result to a html file in "C:\temp\test001" use the following command line:
```
compare.exe --mode=csvFileCompare --verbosity=1 --reportdir="C:\temp\test001" "file_compare.csv" "file_base.csv"
```
To check a folder recursively, compare the CSV files with reference files, a tolerance of 0.004 and report the test-results to the folder "C:\temp\test002", use the following line:
```
compare.exe --mode csvTreeCompare --reportdir "C:\temp\test002" --tolerance 4e-3 "C:\test\2013-05-17-Testrun" "C:\test\comparison-base-files"
```
To check a tree of FMU files against reference CSV files (in the same directory with the same name as the FMU or the name "protocol.csv") use the following command:
```
compare.exe -mode FmuChecker -c "C:\Program Files\FMUChecker-1.0.2-win64\fmuCheck.win64.exe" -r "C:\temp\test003" "C:\test\FMus\2013-05-17_Test"
```
The tool returns 0 if all results were valid and no errors occurred during the validation, 1 if there were invalid results and 2 if there were exceptions or errors during the program run.

## Modes

### CsvFileCompare
Is the default operation mode and compares a given compare file with a base file where "compare " is the value that is to be compared to the base file.

### CsvTreeCompare
A given compare directory is recursively browsed for CSV files. When a compare CSV file has been found the tool searches the second argument, the base directory for the existence of a CSV file with the same name as the compare CSV. If the file is not found the tool searches for a base CSV in the same tree as the compare CSV. I.e.
```
compare.exe -m csvtreecompare c:\temp\compare c:\temp\base
```
When the file "c:\temp\compare\foobar\blubb.csv" has been found the tool searches for "c:\temp\base\blubb.csv" if not found it searches for the file "c:\temp\base\foobar\blubb.csv". That way you can compare whole directory trees.

### FmuChecker
In this mode the tool uses the FMU checker tool. The path to the tool has to be given via command line (and optional the arguments for the checker). The output of the FMU checker is saved to a temporary directory and the directory containing the FMU is searched for a base CSV to compare the output with. At first the tool searches for a CSV file with the same name as the FMU. If not found it searches for a file called "protocol.csv".

If no report directory has been set the compare tool uses the directory of the compare file as the location for the master report and saves the reports for the several checks next to the compare CSV. If set all html files are saved in a flat structure in the given report directory using relative hyperlinks to each other to make it possible to disseminate the reports.

## Hints

CSV (comma-separated values) is a file format that is used by many tools and lacks a proper standardization. [RFC 4180](https://tools.ietf.org/html/rfc4180) describes the basic definitions that are supported by this comparison tool, too.

The default settings for CSV compare to read a CSV file are:
 1. one dataset per line (CRLF or LF does not matter)
 2. first column contains the time and its heading is either "time" or "t"
 3. Default separator is ";" you can set the delimiter with the -d flag
 4. When the CSV contains double values and the delimiter is set to "," the
	double values have to be saved with a "." as decimal separator and/or they
	have to be enclosed in quotation marks (i.e. 0.001 "0,001")
 5.	Column titles are parsed as they are

To receive optimal validation results it is good to use a base file with a better resolution than the compare file, so the tube can be calculated more accurate.

The tool generates html reports that contain scalable vector graphics of all results that are found in both files. Keep that in mind when you compare CSV files with many results as the created html reports can grow too big to be viewed in a browser. The best way is to prepare your base file to contain only results you really want to compare.

To run the tool you need Microsoft Windows and [Microsoft .NET Framework 4.0](https://www.microsoft.com/en-us/download/details.aspx?id=30653) or [Mono (Stable)](http://www.go-mono.com/mono-downloads/download.html) and a Mono-Supported platform.

Alternatively a [Docker](http://docker.com) container can be built and used to run the tool.