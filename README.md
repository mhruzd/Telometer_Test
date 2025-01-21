Install telometer
Telometer GitHub Repo link: https://github.com/santiago-es/Telometer?tab=readme-ov-file 
```{r}
pip install telometer==1.0
```

Navigate to your directory with the telometer files
```{r}
cd desktop/telometer-1.0
```

Download the latest human t2t assembly from https://github.com/marbl/CHM13 (chm13v2.0.fa) 

Append Stong 2014 subtelomere assemblies and index (stong_subtels.fa provided with source):
```{r}
cat chm13v2.0.fa stong_subtels.fa > t2t-and-subtel.fa
```

Use samtools to create the FASTA index (.fai file) 
If there are issues, debug with grep and less to see if and where the issue is 
```{r}
samtools faidx t2t-and-subtel.fa
```

Align basecalled reads to t2t genome with minimap2
SRR28160677.fastq is the .fastq file downloaded from https://trace.ncbi.nlm.nih.gov/Traces/?view=run_browser&page_size=10&acc=SRR28160677&display=download
```{r}
minimap2 -ax map-ont -t 16 t2t-and-subtel.fa SRR28160677.fastq -o output.sam
```
[M::mm_idx_gen::0.558*0.99] collected minimizers
[M::mm_idx_gen::0.607*2.09] sorted minimizers
[M::main::0.607*2.09] loaded/built the index for 43 target sequence(s)
[M::worker_pipeline::12.266*12.82] mapped 13235 sequences
[M::mm_idx_stat] kmer size: 15; skip: 10; is_hpc: 0; #seq: 43
[M::mm_idx_stat::0.672*1.98] distinct minimizers: 2733582 (87.20% are singletons); average occurrences: 1.433; average spacing: 5.376; total length: 21064418
[M::main] Version: 2.28-r1209
[M::main] CMD: minimap2 -ax map-ont -t 16 -o output.sam t2t-and-subtel.fa SRR28160677.fastq
[M::main] Real time: 12.287 sec; CPU: 157.271 sec; Peak RSS: 4.211 GB

Check the output of the output.sam file
```{r}
head output.sam
```

Convert the SAM file to a BAM file using samtools
```{r}
samtools view -Sb output.sam > output.bam
```

Sort the BAM file with samtools
```{r}
samtools sort output.bam -o sorted_output.bam
```

Index the BAM file wih samtools
```{r}
samtools index sorted_output.bam
```

Run telometer
```{r}
telometer -b sorted_output.bam -o output2.tsv
```
Processing final batch of 18183 reads
Telometer completed successfully. Total telomeres measured: 89
Total processing time: 4.65 seconds

Telomere Length Summary Statistics:
Min: 204.00
25th Percentile: 720.00
Median: 876.00
Mean: 2241.80
75th Percentile: 4068.00
Max: 10692.00
