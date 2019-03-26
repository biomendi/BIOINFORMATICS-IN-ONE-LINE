# BIOINFORMATICS IN ONE LINE

Collection of useful commands for bioinformatics purposes. All in one line!

## Fasta 
Convert multiple-line Fasta To single-line Fasta
```bash
awk '/^>/ {printf("\n%s\n",$0);next; } { printf("%s",$0);}  END {printf("\n");}' < INPUT.fasta | tail -n +2 > OUTPUT.fasta 
```
Count bases (A, C, G, T) and missing (N, ?) per sample in single-line Fasta (slow for long files!)
```bash
while read line; do echo $line | grep -v '>' | grep -o "[ACGT]" | sort | uniq -c | paste - - - - | tr "\n" "\t" ;  echo $line | grep -v '>' | grep -o "[?N]" | sort | uniq -c | sort -k2r | paste - - ; echo $line | grep '>' | tr "\n" "\t" ; done < INPUT.fasta
```
Count total number of bases in Fasta (across all sequences)
```bash
grep -v ">" INPUT.fasta | wc | awk '{print $3-$1}'
```

## Phylogenetic trees
Fast way to display a tree in newick format
```bash
ete3 view --text -t TREE.nw
```
Compare tree topologies (a list of trees vs reference tree)
```bash
ete3 compare --src_tree_list TREES.list -r REFERENCE.nw
```

## Others
Count the number of a specific character (e.g. "NA") in each line (prints also the 1st word, delimited by space)
```bash
paste <(while read LINE ; do echo -n "$LINE" | awk -F" " '{print $1}' ; done < INPUT.file) <(awk -F\NA '{print NF-1}' INPUT.file)
```
Compare two unsorted lists
```bash
comm -13 <(sort file1) <(sort file2)
```
