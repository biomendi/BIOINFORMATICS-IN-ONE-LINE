# BIOINFORMATICS IN ONE LINE

Collection of useful commands for bioinformatics purposes. All in one line!

## Fasta / FASTQ
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
Count number of sequences in FASTQ.gz file
```bash
parallel “echo {} && gunzip -c {} | wc -l | awk ‘{d=\$1; print d/4;}’” ::: INPUT.gz
```
Using a list of names (name.list: one sequence name per line), extract sequences from Fasta/FASTQ files (needs seqtk)
```bash
seqtk subseq INPUT.fq name.list > out.fq
```

## Phylogenetic trees
Fast way to display a tree in newick format (needs ete3)
```bash
ete3 view --text -t TREE.nw
```
Compare topology of a list of trees vs reference tree in newick format) (needs ete3)
```bash
ete3 compare --src_tree_list TREES.list -r REFERENCE.nw
```

## SNPs
Count heterozygous SNPs in a beagle file
```bash
echo $(awk ‘{if ($3 != $4) print $3, $4 }’ INPUT.bgl | wc -l )/$total*100 | bc -l
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
