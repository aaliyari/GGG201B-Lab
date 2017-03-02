# 201B - HW2

### Install the MEGAHIT assembler

    git clone https://github.com/voutcn/megahit.git
    cd megahit
    make -j 4

### Download the E. coli data set

    mkdir ~/work
    cd ~/work
    
    curl -O -L https://s3.amazonaws.com/public.ged.msu.edu/ecoli_ref-5m.fastq.gz
    
### Running Trimmomatic

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

4. Run Trimmomatic on the split reads:

        TrimmomaticPE top.R1.fq top.R2.fq \
            trimmed-R1.fq orphan1.fq trimmed-R2.fq orphan2.fq \
            ILLUMINACLIP:TruSeq3-PE.fa:2:40:15 \
            LEADING:2 TRAILING:2 \
            SLIDINGWINDOW:4:2 \
            MINLEN:25
            
    Output:

        Input Read Pairs: 2500000
        Both Surviving: 2496191 (99.85%)
        Forward Only Surviving: 3322 (0.13%)
        Reverse Only Surviving: 461 (0.02%)
        Dropped: 26 (0.00%)
        TrimmomaticPE: Completed successfully
       
### Running MEGAHIT

1. Interleave the trimmed reads:

        interleave-reads.py trimmed-R1.fq trimmed-R2.fq > trimmed-pe.fq
    
2. Assemble the E. coli data set with MEGAHIT:

        ~/megahit/megahit --12 trimmed-pe.fq -o ecoli-trimmed

3. Save the assembly:

        cp ecoli-trimmed/final.contigs.fa ecoli-assembly-trimmed.fa
        
### Measuring the assembly

1. Install [QUAST](http://quast.sourceforge.net/quast):

        cd ~/
        git clone https://github.com/ablab/quast.git -b release_4.2
        export PYTHONPATH=$(pwd)/quast/libs/

2. Run QUAST on the assembly:

        cd ~/work
        ~/quast/quast.py ecoli-assembly-trimmed.fa -o ecoli_report_trimmed

        python2.7 ~/quast/quast.py ecoli-assembly-trimmed.fa -o ecoli_report_trimmed

3. Look at the report in `~/work/ecoli_report_trimmed/report.txt`:

    output:
    
    ```
    All statistics are based on contigs of size >= 500 bp, unless otherwise noted (e.g., "# contigs (>= 0 bp)" and "Total length (>= 0 bp)" include all contigs).

    Assembly                    ecoli-assembly-trimmed
    # contigs (>= 0 bp)         117                   
    # contigs (>= 1000 bp)      93                    
    # contigs (>= 5000 bp)      69                    
    # contigs (>= 10000 bp)     64                    
    # contigs (>= 25000 bp)     52                    
    # contigs (>= 50000 bp)     32                    
    Total length (>= 0 bp)      4577284               
    Total length (>= 1000 bp)   4566196               
    Total length (>= 5000 bp)   4508252               
    Total length (>= 10000 bp)  4471041               
    Total length (>= 25000 bp)  4296074               
    Total length (>= 50000 bp)  3578894               
    # contigs                   102                   
    Largest contig              246618                
    Total length                4572412               
    GC (%)                      50.74                 
    N50                         105708                
    N75                         53842                 
    L50                         15                    
    L75                         30                    
    # N's per 100 kbp           0.00 
    ```

    previous report (untrimmed reads):

    ```
    All statistics are based on contigs of size >= 500 bp, unless otherwise noted (e.g., "# contigs (>= 0 bp)" and "Total length (>= 0 bp)" include all contigs).

    Assembly                    ecoli-assembly
    # contigs (>= 0 bp)         117           
    # contigs (>= 1000 bp)      93            
    # contigs (>= 5000 bp)      69            
    # contigs (>= 10000 bp)     64            
    # contigs (>= 25000 bp)     52            
    # contigs (>= 50000 bp)     32            
    Total length (>= 0 bp)      4577284       
    Total length (>= 1000 bp)   4566196       
    Total length (>= 5000 bp)   4508252       
    Total length (>= 10000 bp)  4471041       
    Total length (>= 25000 bp)  4296074       
    Total length (>= 50000 bp)  3578894       
    # contigs                   102           
    Largest contig              246618        
    Total length                4572412       
    GC (%)                      50.74         
    N50                         105708        
    N75                         53842         
    L50                         15            
    L75                         30            
    # N's per 100 kbp           0.00  
    ```
    
The results are identical! I ran the entire process again and only one of the numbers in QUAST's report changed. This means that the readset we used was clean to begin with and trimming did not have any noticeable effects. This is evident by looking at Trimmomatic's output; 99.85% of the input reads are outputted with their both strands survivng.
