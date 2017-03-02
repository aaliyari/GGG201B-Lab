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
    
3. Run Trimmomatic on your split reads, replacing the filenames in
   `<xyz>` with the appropriate inputs and outputs.

        TrimmomaticPE <r1 input read file> <r2 input read file> \
            <out-r1> <orphan1> out-r2> <orphan2> \
            ILLUMINACLIP:TruSeq3-PE.fa:2:40:15 \
            LEADING:2 TRAILING:2 \
            SLIDINGWINDOW:4:2 \
            MINLEN:25
