## Chip-seq practical session


### Public data, Mikkelsen et al.


#### Fetch fastq


Go on [NCBI GEO](http://www.ncbi.nlm.nih.gov/sra?linkname=bioproject_sra_all&from_uid=125035)

Then select RunSelector.

Chose samples as Lasses' instructions

From a _Cell_ paper from 2010 and belong to this dataset:
 
http://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE20752
 
Specifically I would look at these samples:
 
`3T3L1_t2_H3K4me3`
http://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSM535748
 
`3T3L1_t3_H3K4me3`
http://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSM535755
 
`3T3L1_t2_H3K27ac`
http://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSM535751
 
`3T3L1_t3_H3K27ac`
http://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSM535758
 
I think this is the input control they used for normalization:
http://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSM535740


Download `SRR_Acc_List.txt` in `/work/users/aginolhac/chip-seq/doctoral_school/Mikkelsen`

then fetch and compress

`parallel -j 6 --progress "fastq-dump --gzip {}" :::: SRR_Acc_List.txt`

#### mapped with paleomix

`paleomix bam_pipeline --bwa-max-threads=4 --max-threads=12 mikkelsen.yml`

last for ~ 3 hours 30 minutes



#### peak calling

H3K4

```
macs2 callpeak -tMikkelsen_3T3L1_t2_H3K4me3.GRCm38.p3.bam \
               -c Mikkelsen_3T3L1_WCE.GRCm38.p3.bam \
               -f BAM -g mm -n 3T3L1_t2_H3K4 -B -q 0.01 --outdir 3T3L1_t2_H3K4 &
macs2 callpeak -t Mikkelsen_3T3L1_t3_H3K4me3.GRCm38.p3.bam \
               -c Mikkelsen_3T3L1_WCE.GRCm38.p3.bam \
               -f BAM -g mm -n3T3L1_t3_H3K4 -B -q 0.01 --outdir 3T3L1_t3_H3K4
```

H3K27

```
macs2 callpeak -tMikkelsen_3T3L1_t2_H3K27ac.GRCm38.p3.bam \
               -c Mikkelsen_3T3L1_WCE.GRCm38.p3.bam --broad  \
               -f BAM -g mm -n 3T3L1_t2_H3K27ac -B -q 0.01 --outdir 3T3L1_t2_H3K27ac &
macs2 callpeak -t Mikkelsen_3T3L1_t3_H3K27ac.GRCm38.p3.bam \
               -c Mikkelsen_3T3L1_WCE.GRCm38.p3.bam --broad \
               -f BAM -g mm -n3T3L1_t3_H3K27ac -B -q 0.01 --outdir 3T3L1_t3_H3K27ac
```

