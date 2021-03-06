---
title: "Manipulating and formating GWAS data"
output: 
  html_document:
    toc: true
---



# Introduction 
This is a tutorial document to demonstrate how to make R Markdown writeups.

# Script to manipulate GWAS data

```perl
#Before running, edit path names on lines 18 and 50 to the relevant input files.
#Then run with 'perl Merge_rsIDs.pl'.
#Output will be tab-delimited file named "Merged_rsIDs.txt' in working directory.

#! /usr/bin/perl
use strict;
use warnings;


#Define 2 hashes to store information from files 1 and 2, respectively.
#Hash keys will be joined SNPs (imputed) and rsID columns (first two columns in input files).
#Associated values will be joins of all subsequent fields on the input line.
my %file1hash;
my %file2hash;

if ($ARGV[0]) {
	my $inputfile1 = $ARGV[0];
}

else {
	print "No input file #1 included as command line argument.\n";
	print "Command line should be...\n";
	print "perl Merge_rsIDs.pl input_file1_name input_file2_name output_file_name\n";
	exit;
}

if ($ARGV[1]) {
	my $inputfile2 = $ARGV[1];
}

else {
	print "No input file #2 included as command line argument.\n";
	print "perl Merge_rsIDs.pl input_file1_name input_file2_name output_file_name\n";
	exit;
}

if ($ARGV[2]) {
	my $outputfile = $ARGV[2];
}

else {
	print "No output file included as command line argument.\n";
	print "perl Merge_rsIDs.pl input_file1_name input_file2_name output_file_name\n";
	exit;
}


#Read through file 1 and populate the file 1 hash (store header).
open FILE1, "$inputfile1" or die$!;
my $line = 0;
my $file1header;
my $file1DataToPrint = 0;

while(<FILE1>) {
	if ($line == 0) {
		$line++;
		$file1header = $_;
		$file1header =~ s/\s+/\t/g;
		next;
	}	
	chomp($_);
	my @TempArray = split(/\s+/ , $_);
	my $joinedname = $TempArray[0]."\t".$TempArray[1];
	my $ArrayLength = @TempArray;
	if ($ArrayLength > 2) {
		my $restofarray = join("\t",@TempArray[2..$ArrayLength-1]);
		$file1hash{$joinedname} = $restofarray;
		$file1DataToPrint = 1;
	}

	else {
		$file1hash{$joinedname} = "NA";
		
	}	
}

close FILE1;
	

#Read through file 2 and populate the file 2 hash (store header).
open FILE2, "$inputfile2" or die$!;

$line = 0;
my $file2header;
my $editedfile2header;
my $file2DataToPrint = 0;

while(<FILE2>) {
	if ($line == 0) {
		$line++;
		$file2header = $_;
		$file2header =~ s/\s+/\t/g;
		next;
	}	
	chomp($_);
	my @TempArray = split(/\s+/ , $_);
	my $joinedname = $TempArray[0]."\t".$TempArray[1];
	my $ArrayLength = @TempArray;
	if ($ArrayLength > 2) {
		my $restofarray = join("\t",@TempArray[2..$ArrayLength-1]);
		$file2hash{$joinedname} = $restofarray;
		$file2DataToPrint = 1;
	}

	else {
		$file1hash{$joinedname} = "NA";
		
	}
	
}

close FILE2;

#create the output file
open MERGED, ">$outputfile" or die$!;

#print the header to the output file.
#Have to get rid of the repeated column names first.
my @TempHeader2 = split(/\t/, $file2header);
shift @TempHeader2;
shift @TempHeader2;
$editedfile2header = join("\t", @TempHeader2);		

print MERGED "$file1header\t$editedfile2header\n";


#Go through each key from hash 1 (file 1 joined SNP names) and check to see if that joined name occurs in hash 2.
#If there's a match, print the non-redundant information from the two files (hash key and its values from both hashes) to output file.
#Might need updated if both files only contained the first two columns - assuming this wouldn't ever happen?

for (keys %file1hash) {
	if ($file2hash{$_}) {
		my $joinedrsIDinfo;
		
		if ($file1DataToPrint == 0)  {
			$joinedrsIDinfo = $_."\t".$file2hash{$_};
			print MERGED "$joinedrsIDinfo\n";
		}
		
		elsif ($file1DataToPrint == 0) {
			$joinedrsIDinfo = $_."\t".$file1hash{$_};
			print MERGED "$joinedrsIDinfo\n";
		}
		
		else {
		
			$joinedrsIDinfo = $_."\t".$file1hash{$_}."\t".$file2hash{$_};
			print MERGED "$joinedrsIDinfo\n";
		}
		
	}
}

close MERGED;
```

# Other things RMarkdown can do
You can write LaTeX equations:
$$ RSS = (\hat{y} - y_{i})^2 $$
You can make ordered lists. The caveat is that you need two spaces after the lines to have a line break, and also you need proper spacing to make sub lists.

- Here is a list:  
  - This the first element on that list  
  - Second 
    - Third
    

You can embed HTML links [github](github.com)

You can **bold** things .. you make them *italics*

And you can add R code


```r
data(iris)
head(iris)
```

```
##   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
## 1          5.1         3.5          1.4         0.2  setosa
## 2          4.9         3.0          1.4         0.2  setosa
## 3          4.7         3.2          1.3         0.2  setosa
## 4          4.6         3.1          1.5         0.2  setosa
## 5          5.0         3.6          1.4         0.2  setosa
## 6          5.4         3.9          1.7         0.4  setosa
```

```r
knitr::kable(head(iris))
```



| Sepal.Length| Sepal.Width| Petal.Length| Petal.Width|Species |
|------------:|-----------:|------------:|-----------:|:-------|
|          5.1|         3.5|          1.4|         0.2|setosa  |
|          4.9|         3.0|          1.4|         0.2|setosa  |
|          4.7|         3.2|          1.3|         0.2|setosa  |
|          4.6|         3.1|          1.5|         0.2|setosa  |
|          5.0|         3.6|          1.4|         0.2|setosa  |
|          5.4|         3.9|          1.7|         0.4|setosa  |

# Session info

```r
sessionInfo()
```

```
## R version 3.4.1 (2017-06-30)
## Platform: x86_64-apple-darwin15.6.0 (64-bit)
## Running under: macOS Sierra 10.12.6
## 
## Matrix products: default
## BLAS: /System/Library/Frameworks/Accelerate.framework/Versions/A/Frameworks/vecLib.framework/Versions/A/libBLAS.dylib
## LAPACK: /Library/Frameworks/R.framework/Versions/3.4/Resources/lib/libRlapack.dylib
## 
## locale:
## [1] en_US.UTF-8/en_US.UTF-8/en_US.UTF-8/C/en_US.UTF-8/en_US.UTF-8
## 
## attached base packages:
## [1] stats     graphics  grDevices utils     datasets  methods   base     
## 
## other attached packages:
## [1] knitr_1.17
## 
## loaded via a namespace (and not attached):
##  [1] compiler_3.4.1  backports_1.1.0 magrittr_1.5    rprojroot_1.2  
##  [5] htmltools_0.3.6 tools_3.4.1     yaml_2.1.14     Rcpp_0.12.12   
##  [9] stringi_1.1.5   rmarkdown_1.8   highr_0.6       stringr_1.2.0  
## [13] digest_0.6.12   evaluate_0.10.1
```
