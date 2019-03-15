# Bioinformatics in one line

Collection of useful commands for bioinformatics purposes.

## FASTA 

Convert multiple-line Fasta To single-line Fasta

```bash
awk '/^>/ {printf("\n%s\n",$0);next; } { printf("%s",$0);}  END {printf("\n");}' < INPUT.fasta | tail -n +2 > OUTPUT.fasta 
```

Count bases (A, C, G, T) and missing data (N, ?) in single-line Fasta

```bash
while read line; do echo $line | grep -v '>' | grep -o "[ACGT]" | sort | uniq -c | paste - - - - | tr "\n" "\t" ;  echo $line | grep -v '>' | grep -o "[?N]" | sort | uniq -c | sort -k2r | paste - - ; echo $line | grep '>' | tr "\n" "\t" ; done < INPUT.fasta
```

Count total number of bases in Fasta (across all sequences)

```bash
grep -v ">" INPUT.fasta | wc | awk '{print $3-$1}'
```
