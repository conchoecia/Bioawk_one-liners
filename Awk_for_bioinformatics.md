# Awk for bioinformatics

Here are a collection of useful one-liners for bioinformatics.

# Examples

### Get the mean coverage and length of each reference sequence in a BAM file

This requires [`bedtools genomecov`](http://bedtools.readthedocs.io/en/latest/content/tools/genomecov.html). Outputs a file with the reference fasta sequence, the average per-site coverage of the reference, and the reference length. For example, here are a few lines of output:

```
tig00011346|arrow|pilon 18.95   33242
tig00099193|arrow|pilon 10.82   13916
tig00009247|arrow|pilon 8.17    3997
tig00007946|arrow|pilon 13.16   12590
tig00003447|arrow|pilon 24.66   32315
```

- Command 
  - `bedtools genomecov -ibam file.bam -d | awk '{arr[$1] += $3; count[$1] += 1} END{ for (a in arr) {printf "%s\t%.2f\t%d\n", a, arr[a] / count[a], count[a] } }'`
  
### Get the coverage at each site and the length of the reference
 
This requires [`bedtools genomecov`](http://bedtools.readthedocs.io/en/latest/content/tools/genomecov.html). This outputs the reference fasta sequence, the coverage at a single site, and the total length of the reference. This is useful for plotting the coverage on the x-axis and the reference length on the y-axis in a marginal histogram (for example using [`pauvre custommargin`](https://github.com/conchoecia/pauvre).

```
tig00011346|arrow|pilon 18   33242
tig00011346|arrow|pilon 19   33242
tig00011346|arrow|pilon 20   33242
tig00011346|arrow|pilon 20   33242
```

- Command
  - `bedtools genomecov -ibam file.bam -d | gawk '{arr[$1][$2] = $3; len[$1] += 1} END{ for (a in arr) { for (element in arr[a]) {printf "%s\t%d\t%d\n", a, arr[a][element], len[a] }} }'`
