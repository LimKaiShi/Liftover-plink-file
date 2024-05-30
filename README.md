# Liftover-plink-file
This repository will bring you how to liftover plink file format (.bim .bed .fam) from hg38 to hg19 and vice versa

The lift over will be using pipeline developed by sritchie73 https://github.com/sritchie73/liftOverPlink/tree/master

# Getting started with 
## File needed
1. 2 Python script from sritchie73
   - liftOverPlink.py
   - rmBadLifts.py
2. UCSC liftover executable
   - Download from: https://genome-store.ucsc.edu/products/
   - Guide to download directly from terminal (not only liftover but also other utilities): http://hgdownload.soe.ucsc.edu/downloads.html#liftover
3. Chain file (file that guide liftover how to lift from what version to what version, eg: hg38 to hg19)
   - Download from: http://hgdownload.soe.ucsc.edu/downloads.html#liftover
   - Go to Human Genome > Dec. 2013 (GRCh38/hg38) or any > LiftOver files > Find file with name "hg38ToHg19.over.chain.gz"
   - I have upload the different chain files into this repository as well for convenience
4. Your plink file! (.bim .bed .fam)

## 1. Download all the files
### 1.1 Python script from sritchie73 https://github.com/sritchie73/liftOverPlink/tree/master

```
%%bash 
cd /PATH/TO/YOUR/DIRECTORY
#clone the github repository
git clone https://github.com/sritchie73/liftOverPlink.git
```
Now you should have 2 python files in your directory which are ```rmBadLifts.py``` and ```liftOverPlink.py```

Try running the program to test it has been installed properly or not
```
!python /home/jupyter/QC/utility/liftOverPlink/liftOverPlink.py
```
- if ok the output for ```liftOverPlink.py``` will be:
```
usage: liftOverPlink.py [-h] -m MAPFILE [-p PEDFILE] [-d DATFILE] -o PREFIX -c
                        CHAINFILE [-e LIFTOVEREXECUTABLE]

liftOverPlink.py converts genotype data stored in plink's PED+MAP format from
one genome build to another, using liftOver.

options:
  -h, --help            show this help message and exit
  -m MAPFILE, --map MAPFILE
                        The plink MAP file to `liftOver`.
  -p PEDFILE, --ped PEDFILE
                        Optionally remove "unlifted SNPs" from the plink PED
                        file after running `liftOver`.
  -d DATFILE, --dat DATFILE
                        Optionally remove "unlifted SNPs" from a data file
                        containing a list of SNPs (e.g. for --exclude or
                        --include in `plink`)
  -o PREFIX, --out PREFIX
                        The prefix to give to the output files.
  -c CHAINFILE, --chain CHAINFILE
                        The location of the chain file to provide to
                        `liftOver`.
  -e LIFTOVEREXECUTABLE, --bin LIFTOVEREXECUTABLE
                        The location of the `liftOver` executable.

```
- for ```rmBadLifts.py``` will be:
```
usage: rmBadLifts.py [-h] -m MAPFILE -o OUTFILE -l LOGFILE

rmBadLifts.py goes through a plink MAP file, identifies and removes SNPs not
on chromosomes 1 to 22

options:
  -h, --help            show this help message and exit
  -m MAPFILE, --map MAPFILE
                        MAP file to process.
  -o OUTFILE, --out OUTFILE
                        New MAP file to output.
  -l LOGFILE, --log LOGFILE
                        File to output the bad rsIDs to.

```

#### 1.1.1 Problem faced
There is problem for ```liftOverPlink.py``` after I run it it shows:
```
print msg should have parenthesis ()
print "Converting MAP file to UCSC BED file..." should have parenthesis ()
```
Solution: 
- Just adit ```liftOverPlink.py``` manually in terminal
- At terminal type:
```
nano /PATH/TO/liftOverPlink.py
```
-  Add () to all print command line. eg: print "Converting MAP file to UCSC BED file..." become print("Converting MAP file to UCSC BED file...")
-  Save changes
-  After that should be ok

<a id="1.2"></a>
### 1.2 UCSC liftover executable 
#### 1.2.1 Option 1: Download via terminal (recommended)
```
%%bash
wget https://hgdownload.cse.ucsc.edu/admin/exe/linux.x86_64/liftOver
chmod +x ./filePath/liftOver
./filePath/liftOver
```
#### 1.2.2 Option 2: Download via web site
- Register yourself first at https://genome-store.ucsc.edu/products/
- Then find liftover and directly download it

#### 1.2.3 Problem faced
1. If you are using older mac version you might get this
```
  dyld: Symbol not found: __ZNKSt3__115basic_stringbufIcNS_11char_traitsIcEENS_9allocatorIcEEE3strEv
  Referenced from: /Users/limkaishi/Downloads/./liftOver-4 (which was built for Mac OS X 14.0)
  Expected in: /usr/lib/libc++.1.dylib

zsh: abort      ./liftOver-4
```
or 
```
zsh: exec format error ./liftOver
```
- Reason: It is uncompatible
- Solution: Try to build the executable from source code (refer here: https://groups.google.com/a/soe.ucsc.edu/g/genome/c/C0ByF1YbK_4?pli=1)

### 1.3 Chain file
- Download from: http://hgdownload.soe.ucsc.edu/downloads.html#liftover
- Can try directly download from terminal as well, but I found it sometimes take a very long time
```
%%bash
wget ftp://hgdownload.soe.ucsc.edu/goldenPath/hg38/liftOver/hg38ToHg19.over.chain.gz
```

## 2. Lift Over the file
### 2.1 Convert .bim .bed .fam inro .map and .ped
```
%%bash
WORK_DIR='/PATH/TO/DIRECTORY'
cd $WORK_DIR

/home/jupyter/plink1.9 \
--bfile /YOUR/BIM-BED-FAM_PRE-FIX \
--recode \
--out /OUTPUT/NAME
```
- After runnning this, you whould get your ```.map``` and ```.ped``` file
### 2.2 Run the ```liftOverPlink.py``` to get your lifted ```.map``` file
```
!python /home/jupyter/QC/utility/liftOverPlink/liftOverPlink.py --map /home/jupyter/QC/GP2_merge_all_updated.map --out /home/jupyter/QC/GP2_merge_all_updated_lifted --chain /home/jupyter/QC/hg38ToHg19.over.chain.gz -e /home/jupyter/QC/utility/liftOver 
```
- ```--map``` add your .map file
- ```--out``` Give your output file a name and path
- ```--chain``` add the chain file you wanted to use
- ```-e``` add the path to liftover executable downloaded at 1.2

After that you will have 2 files
- ```lifted.map``` file which is the map file you will be using for ```rmBadLifts.py``` (only SNPs that can be lifted are here)
- ```.bed.unlifted``` file which contain SNPs that fail to be lifed which later [will be removed] from ```.ped``` file

### 2.3 Run the ```rmBadLifts.py``` to futher removed bad SNPs from ```lifted.map file```
```
!python /home/jupyter/QC/utility/liftOverPlink/rmBadLifts.py --map /home/jupyter/QC/GP2_merge_all_updated_lifted.map --out /home/jupyter/QC/GP2_merge_all_updated_lifted_good.map --log /home/jupyter/QC/bad_lifted.dat
```
- ```--map``` add your lifted.map file
- ```--out``` give your final lifted .map file a name
- ```--log``` give those additional unable to lifted SNPs file a name (Additional bad SNPs)
After that you will have another 2 files"
- ```final.lifted.map``` file which is the final map file you will be using
- ```bad_lifted.dat``` file which contain additional bad SNPs [will be removed] from ```.ped``` file

### 2.4 Prepare ```.ped``` file
#### 2.4.1 Prepare bad SNPs list from ```.bed.unlifted``` and ```bad_lifted.dat``` file
- How those 2 file look like?
- ```.bed.unlifted```, 
- ```bad_lifted.dat```, which you can see the second column is the SNPs list
```
8_gl000196_random	JHU_8.48229038	0.0	2819
8_gl000196_random	JHU_8.48228799	0.0	2580
8_gl000196_random	JHU_8.48228030	0.0	1811
8_gl000196_random	JHU_8.48227941	0.0	1722
```
```
%%bash
WORK_DIR='/home/jupyter/QC'
cd $WORK_DIR

cut -f 2 /home/jupyter/QC/bad_lifted.dat > /home/jupyter/QC/to_exclude.dat
cut -f 4 /home/jupyter/QC/GP2_merge_all_updated_lifted.bed.unlifted | sed "/^#/d" >> /home/jupyter/QC/to_exclude.dat 

```

