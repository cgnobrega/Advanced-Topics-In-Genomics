# Generalized pipeline for NGS analysis, using variant detection as example
    1. quality check
	- FastQC (https://www.bioinformatics.babraham.ac.uk/projects/fastqc/)
	- multiqc (https://multiqc.info)
    2. trimming
	- trimmomatic ((http://www.usadellab.org/cms/?page=trimmomatic)
	- trimgalore (https://github.com/FelixKrueger/TrimGalore)
	- Skewer (https://sourceforge.net/projects/skewer/)
    3. Removing duplicates
	- Piccard (https://broadinstitute.github.io/picard/)
	- FastUniq (https://github.com/alces-software/packager-base/blob/master/apps/fastuniq/1.1/fastuniq.sh.md)
    4. aligning and assembling
	- BWA-mem (http://bio-bwa.sourceforge.net/bwa.shtml)
	- Bowtie2 (http://bowtie-bio.sourceforge.net/bowtie2/index.shtml)
	- Novoalign (http://www.novocraft.com/products/novoalign/)
	- TopHat (RNAseq) (https://ccb.jhu.edu/software/tophat/index.shtml)
	- STAR (RNAseq) (https://github.com/alexdobin/STAR)
	- GSNAP (RNAseq) (https://bioinformaticshome.com/tools/rna-seq/descriptions/GSNAP.html)
	- Kallisto (RNAseq) (https://github.com/pachterlab/kallisto)
    5. variant detection
	- Samtools (https://www.htslib.org)/
	- BCFtools (https://samtools.github.io/bcftools/bcftools.html)
	- BAMtools (https://github.com/pezmaster31/bamtools/wiki)
	- FreeBayes (https://github.com/freebayes/freebayes)
	- ANGSD (http://www.popgen.dk/angsd/index.php/ANGSD)
	- GATK (https://gatk.broadinstitute.org/hc/en-us)
	- VarScan2 (http://varscan.sourceforge.net)
	- sam/bam file manipulation
	- SAMtools (http://samtools.sourceforge.net)
    6. variant analysis
	- SNPEff (http://pcingola.github.io/SnpEff/)
	- ANGSD (http://www.popgen.dk/angsd/index.php/ANGSD)
    7. variant viewing, manipulation, and storage
	- Integrative Genomics Viewer  (https://igv.org)
	- vcfR: VCF data in R: (https://github.com/knausb/vcfR)
