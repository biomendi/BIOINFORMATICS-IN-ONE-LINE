# BIOINFORMATICS-IN-ONE-LINE

Collection of useful commands for bioinformatics purposes

### Convert multiple-line Fasta To single-line Fasta

```bash
awk '/^>/ {printf("\n%s\n",$0);next; } { printf("%s",$0);}  END {printf("\n");}' < INPUT.fasta | tail -n +2 > OUTPUT.fasta 
```


