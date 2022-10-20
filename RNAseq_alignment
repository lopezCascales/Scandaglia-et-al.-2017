#!/bin/bash
#Made by MT Lopez-Cascales
#Made on 27/07/2022



######################################

#### RNAseq RAW DATA PROCESSING ####

# GTFfile Mus_musculus.GRCm38.83.gtf

######################################
# script to build reference genome for STAR (STAR v2.6.1a_08-27)
##[STAR] Spliced Transcripts Alignment to a Reference © Alexander Dobin, 2009-2015

# References: 
	# STAR: ultrafast universal RNA-seq aligner. Alexander Dobin. Bioinformatics (2012) 
	# STAR manual 2.5.0a Alexander Dobin Nov, 2015
#####################################

#### Alignment generate index genome mm10. 
STAR --runMode genomeGenerate --genomeDir ./indexmm10 --genomeFastaFiles ./*.fa --sjdbGTFfile ./*.gtf --runThreadN 4 --sjdbOverhang 74 --genomeSAsparseD 2 --genomeSAindexNbases 13

#### Samples Alignment to genome 
STAR --genomeDir ./indexmm10 --readFilesIn file.fastq.gz  --chimSegmentMin 1 -quant -outSJfilterReads -outWigNorm wiggle -outSAMtype BAM SortedByCoordinate -outReadsUnmapped Fastx  --outSAMunmapped Within --quantMode GeneCounts --sjdbGTFfile Mus_musculus.GRCm38.83.gtf  --runThreadN 20  --outFileNamePrefix file


#####################################

#Step only for single end

### How to split bam files by strand 
# https://www.biostars.org/p/92935/
# https://www.biostars.org/p/196746/#196755
# https://www.biostars.org/p/179035/

for file in ./*.bam
do
echo $file 
samtools view -b -F 16 $file > ${file/.bam/_rev.bam}
done

for file in ./*.bam
do
echo $file 
samtools index $file
done

for file in ./*.bam
do
echo $file 
samtools view -b -f 16 $file > ${file/.bam/_fwd.bam}
done

for file in ./*fwd.bam
do
echo $file 
samtools index $file
done


#####################################
	
# Sam to bam
for file in ./.*.sam;
do samtools view -bS $file > ${file/.sam/.bam};
done  

#####################################

# filter by quality

for file in ./.*.bam;
do echo $file
samtools view -b -q 10 $file > ${file/.bam/filtered.bam}; 
done

#####################################

# sort by position

for file in ./.*filtered.bam; 
do 
samtools sort $file > ${file/.filtered.bam/sortedPos.bam};
done

#####################################

# samtools index

for file in ./.*SortedPos.bam; 
do 
samtools index $file;
done

#####################################

# Combine alignments that originate on the same tipe
#
samtools merge -f fileMERGEoutput.bam input1.bam input2.bam ... inputX.bam
samtools index fileMERGEoutput.bam

#####################################

# Obtain a bw files (RPKM normalized)

bamCoverage -of bigwig -bs 10 --normalizeUsing RPKM -b file.bam -o file.bw -p 20

for file in ./.*SortedPos.bam; 
do 
bamCoverage -of bigwig -bs 10 --normalizeUsing RPKM -b $file -o ${file/.bw} -p 20;
done

for file in ./.*MERGE.bam; 
do 
bamCoverage -of bigwig -bs 10 --normalizeUsing RPKM -b $file -o ${file/merge.bw} -p 20;
done


#####################################
for file in ./.*MERGE.bam; 
do 
samtools index $file;
done
bamCoverage -of bigwig -bs 10 --normalizeUsing RPKM -b 01LD_MERGE.bam -o 01LD_MERGE.bw -p 20
