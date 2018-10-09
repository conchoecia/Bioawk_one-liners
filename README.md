# Bioawk one-liners
Useful commands for bioawk for bioinformaticians.

[Bioawk](https://github.com/lh3/bioawk) is a useful tool for parsing biological data on the command line on unix systems. 

Working with tools like `awk`, `uniq`, `cut`, `sed`, and other simple tools on the command line are often the fastest way to get an idea of your data and often sufficient for simple analyses. This is faster than writing a new script for each analysis that you would like to do.

Below are examples of useful one-liners that I often need or have found useful. Many of these are not immediately obvious to new `awk` users, but hopefully they are useful! For more tips, @vsbuffalo [has a great tutorial here](https://github.com/vsbuffalo/bioawk-tutorial).

## Examples - fasta/fastq

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

- Get the longest sequence length in a fasta/q file
  - adapted from `https://unix.stackexchange.com/questions/24509`

```
bioawk -cfastx 'length($seq) > max_length {max_length = length($seq)} END{print max_length}' myfile.fastx
```

- Get a ACGNT-by-position count of fastq reads. Useful for looking at biases in a subset of sequences

```
bioawk -cfastx 'BEGIN{for (i=1; i<=150; i++){A[i]=0; C[i]=0; G[i]=0; N[i]=0; T[i]=0}} {for ( i = 1; i <= length($seq); i++) {thisChar = substr($seq, i,1); if (thisChar=="A"){A[i]+=1} else if (thisChar=="C"){C[i]+=1} else if (thisChar=="G"){G[i]+=1} else if (thisChar=="N"){N[i]+=1} else if (thisChar=="T"){T[i]+=1} }} END{printf("A\tC\tG\tN\tT\n"); for (i=1; i<=150; i++) {printf("%s\t%s\t%s\t%s\t%s\n", A[i], C[i], G[i], N[i], T[i])}}'
```

- Same thing as above but works with sequences only as input. Good for grepping sequences out of a fastq and analyzing that subset

```
bioawk -cfastx '{print($seq)}' reads.fastq.gz | grep "GATC" | head | awk 'BEGIN{for (i=1; i<=150; i++){A[i]=0; C[i]=0; G[i]=0; N[i]=0; T[i]=0}} {for ( i = 1; i <= length($0); i++) {thisChar = substr($0, i,1); if (thisChar=="A"){A[i]+=1} else if (thisChar=="C"){C[i]+=1} else if (thisChar=="G"){G[i]+=1} else if (thisChar=="N"){N[i]+=1} else if (thisChar=="T"){T[i]+=1} }} END{printf("A\tC\tG\tN\tT\n"); for (i=1; i<=150; i++) {printf("%s\t%s\t%s\t%s\t%s\n", A[i], C[i], G[i], N[i], T[i])}}'
```
