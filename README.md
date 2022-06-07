# BIOINFORMATICS IN ONE LINE

In biology, there are many ways to solve a task. However, biologists sometimes need simple ways to get easy tasks done quick - without actually compromising efficiency and accuracy. If that is what you need, the following list might help you. Below I have gathered some commands which I did wrote or find useful to solve different bioinformatics problems. Be aware that some of these scripts are perhaps a bit too specific, so bear that in mind when using them for your own work.    


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
Using a list of identifiers (one sequence name per line), extract sequences from Fasta/FASTQ files (needs seqtk)
```bash
seqtk subseq INPUT.fq name.list > out.fq
```
Parse a Fasta (useful to modify identifiers) 
```bash
awk '/^>/{printf $0"\n",++i; next}{print}' INPUT.fasta
```
Sequence length from Fasta 
```bash
cat INPUT.fasta|awk '$0 ~ ">" {if (NR > 1) {print c;} c=0;printf substr($0,2,200) "\t"; } $0 !~ ">" {c+=length($0);} END { print c; }'
```

## Alignments
For a particular sample in a phylip alignment, count occurrences of a single character (eg. "A")
```bash
grep "SAMPLE" ALIGNMENT.phylip | awk '{print $2}' | awk -F"A" '{print NF-1}' 
```

## Phylogenetic trees
Fast way to display a tree in newick format (needs 'ete3')
```bash
ete3 view --text -t TREE.nw
```
Compare topology of a list of trees vs reference tree (needs 'ete3')
```bash
ete3 compare --src_tree_list TREES.list -r REFERENCE.nw
```
Reroot newick tree (needs 'newick_utils')
```bash
nw_reroot -l INPUT.tree OUTGROUP
```


## SNPs
Count heterozygous SNPs in a beagle file
```bash
echo $(awk ‘{if ($3 != $4) print $3, $4 }’ INPUT.bgl | wc -l )/$total*100 | bc -l
```

## Others 
#### General use:
Compare two unsorted lists
```bash
comm -13 <(sort file1) <(sort file2)
```
Delete all files with size 0 in current directory
```bash
find . -type f -empty -delete
```
Add extension (e.g. ".tre") to multiple files
```bash
find . -type f -print0 | xargs -0 -I{} mv "{}" "{}".tre
```
Remove extension (e.g. ".txt") for multiple files
```bash
find -type f -name '*.txt' | while read f; do mv "$f" "${f%.txt}"; done
```
Fast way (multi-core) to compress or decompress big files (using pigz)
```bash
tar cf - paths-to-archive | pigz -9 -p 32 > archive.tar.gz
```
```bash
pigz -dc target.tar.gz | tar xf -
```

#### Specific use: 
Count the number of a specific character (e.g. "NA") in each line (prints also the 1st word, delimited by space)
```bash
paste <(while read LINE ; do echo -n "$LINE" | awk -F" " '{print $1}' ; done < INPUT.file) <(awk -F\NA '{print NF-1}' INPUT.file)
```
