# Bioawk one-liners
Useful commands for bioawk for bioinformaticians.

[Bioawk](https://github.com/lh3/bioawk) is a useful tool for parsing biological data on the command line on unix systems. 

Working with tools like `awk`, `uniq`, `cut`, `sed`, and other simple tools on the command line are often the fastest way to get an idea of your data and often sufficient for simple analyses. This is faster than writing a new script for each analysis that you would like to do.

Below are examples of useful one-liners that I often need or have found useful. Many of these are not immediately obvious to new `awk` users, but hopefully they are useful! For more tips, @vsbuffalo [has a great tutorial here](https://github.com/vsbuffalo/bioawk-tutorial).

## Examples

- Convert a `fasta` to `fastq`, inserting `!` characters for the base quality scores. 
  - This is useful for PacBio Sequel data (since there are no quality scores), and instances in which a parser requires the seqs from a `fastq` but doesn't use quality information.

```
bioawk -cfastx '{s=sprintf("%*s",length($seq),"");gsub(/ /,"!",s); printf  "@%s\n%s\n+\n%s\n", $name, $seq, s}' reads.fa
```

- Convert a single-line `fasta` to a multiline `fasta`.
  - This is extremely slow. Try `reformat.sh` in [bbmap](https://sourceforge.net/projects/bbmap/) instead!
  - `reformat.sh in=your_file.fa out=new_file.fa fastawrap=NN`

```
bioawk -cfastx '{system("printf \">%s\"" $name " >> multiline.fasta"); system("echo '' >> multiline.fasta"); system("echo " $seq " | fold -w 60 - >> multiline.fasta") }' singleline.fasta
```
