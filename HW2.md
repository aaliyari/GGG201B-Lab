### Install the MEGAHIT assembler

    git clone https://github.com/voutcn/megahit.git
    cd megahit
    make -j 4

### Download the E. coli data set

    mkdir ~/work
    cd ~/work
    
    curl -O -L https://s3.amazonaws.com/public.ged.msu.edu/ecoli_ref-5m.fastq.gz
    
## Running Trimmomatic

1. Install Trimmomatic:

        sudo apt-get -y install trimmomatic

2. Download the TruSeq3-PE adapters:

        wget https://anonscm.debian.org/cgit/debian-med/trimmomatic.git/plain/adapters/TruSeq3-PE.fa
            
3. Read manipulation

Install khmer:

    pip install khmer==2.0

Split the reads into 'left' and 'right' reads:

    gunzip -c ecoli_ref-5m.fastq.gz | \
        split-paired-reads.py -1 top.R1.fq -2 top.R2.fq

You can interleave them again by doing:

    interleave-reads.py top.R1.fq top.R2.fq > top-pe.fq

4. Run Trimmomatic on the split reads:

        TrimmomaticPE top.R1.fq top.R2.fq \
            trimmed-R1.fq orphan1.fq trimmed-R2.fq orphan2.fq \
            ILLUMINACLIP:TruSeq3-PE.fa:2:40:15 \
            LEADING:2 TRAILING:2 \
            SLIDINGWINDOW:4:2 \
            MINLEN:25
            
Output:

        Input Read Pairs: 2500000 Both Surviving: 2496191 (99.85%) Forward Only Surviving: 3322 (0.13%) Reverse Only Surviving: 461 (0.02%)
        Dropped: 26 (0.00%)
        TrimmomaticPE: Completed successfully
        
