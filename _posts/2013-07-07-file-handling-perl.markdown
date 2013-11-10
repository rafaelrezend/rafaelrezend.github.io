---
layout: post
title: File handling in Perl through a simple sort example
---

When it comes to text parsing, I always stick with Perl for a simple and fast implementation. Often referred as *"the Swiss Army chainsaw of scripting languages"*, Perl can be very handy to interpret plain text, logs and even perform model transformations.

The common usage often requires reading an writing to files, which is merely a recipe widespread around the internet.
So, as you can guess, the content below is not new at all, but my intention is to keep a reference of syntax and common features for my own future usage.

The example below is quite simple: a Perl script process an input file which contains a sequence of integers [CSV](http://en.wikipedia.org/wiki/Comma-separated_values) format, and writes an output file with the ordered sequence of those numbers.

So, starting from the input parameters of the Perl script and the creation of a handler:

```perl
# checking for two parameters: input and output files
if ($#ARGV != 1) {
	print "usage: ./SortNumbers <input_file> <out_file>\n";
	exit;
}

# getting input parameters into variables
my $input_file         = shift(@ARGV);
my $output_file        = shift(@ARGV);

# filenames can be easily printed as follows
print "Input file is: $input_file\n";
print "Output file is: $output_file\n";

# trying to open the input file
# if the input file cannot be read, the application exits
open FILE, "< $input_file" or die $!;

# reading file (multiple lines allowed)
my $input_data;
while (<FILE>) {
	$input_data.=$_;
}

# close input reader
close FILE;
```

Input parameters are contained in the `@ARGV` array.
So, the first line checks if the index of the last position of this array (`$#ARGV`) is equals to 1, that is, the array `@ARGV` has exactly two values.
The `shift(@array)` function then pulls the first value out of the array.

Afterwards, the script tries to open the file handler `FILE` for the given filename in `$input_file`.
If the attempt fails, the script exits with an errorcode and message.
Otherwise, the content of the input file is concatenated into `$input_data` variable before the file handler is closed.

The block below starts with a function call `&sort_input()` which will be briefly discussed at the end.

```perl
# call a subroutine to validate the input, split and sort
my $output_data = &sort_input($input_data);

# open output file
open OUTPUT, "> $output_file";

# write to output file
print OUTPUT "$output_data\n";

# close output writer
close OUTPUT;
```

As seen above, the process of writing to file is very similar to reading.
The operator `>` indicates that the content will be overwritten into the output file, while `>>` would concatenate instead.

Finally the subroutine `sort_input()` is shown below:

```perl
# subroutine to validate the input, split and sort values
sub sort_input {

	my $input_data = $_[0];

	# remove endlines and carriage return
	$input_data =~ tr/\r//d;
	$input_data =~ tr/\n//d;

	# check if the input data contains only numbers and single commas
	# if not, the application exits
	if ($input_data !~ /^\d+(,\d+)*$/) {
		print "Error: invalid input file.\n";
		exit;
	}

	# split the input where there are commas and store into array
	my @input_array = split(',', $input_data);

	# sort values from the array
	my @sorted_array = sort {$a <=> $b} @input_array;

	# join values placing comma in between them
	return join(",", @sorted_array);
}
```

`@_` hold the parameters of the `sort_input` function.
After, the function `shift(@_)` could be used instead of `$_[0]` to get the first value from the `@_` array.
They are just different ways of obtaining values from an array, being the first one destructive while the last one keeps the original array.

The content of the `$input_data` is validated throught the regular expression [`/^\d+(,\d+)*$/`](http://regexpal.com/?flags=g&regex=^\d%2B%28%2C\d%2B%29*%24&input=12%2C3%2C56%2C555%2C78%2C0): the value should contain only integers with a single comma in between.

The function `split()` is self-explanatory as well as its counter-operation `join()`.
The `sort()` function sorts the elements of a given array alphabetically. Therefore, the operator `<=>` forces the `sort` function to perform a numerical comparison.
If this operator is omitted, the `sort` function would place 12345 before 23, for instance.

The complete code above can be placed in a single **.pl** file. An example of execution is shown below:

```bash
$ echo "1,23,44,567,32,12,1,7,90,123,1024" > input.csv

$ ./SortNumbers.pl input.csv output.csv
Input file is: input.csv
Output file is: output.csv

$ cat output.csv
1,1,7,12,23,32,44,90,123,567,1024
```

More complete examples of Perl scripts can be found in my [repository](https://github.com/rafaelrezend/MiscScripts/tree/master/Perl).
