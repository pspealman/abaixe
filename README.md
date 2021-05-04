# abaixe
BETA - Pipeline for the positional analysis of ribosome profiling data

Example:

# Step one: Build experiment object from map file, parse input sam files
python /scratch/ps163/Carolina_03_18_2021/abaixe/abaixe.py -sam -map /scratch/ps163/Carolina_03_18_2021/metadata/map_file.tsv \
    -filter /scratch/ps163/Carolina_03_18_2021/metadata/filter_regions.tab \
    -o /scratch/ps163/Carolina_03_18_2021/pickles/ \
    -experiment /scratch/ps163/Carolina_03_18_2021/pickles/cnv_experiment.p
    
# Step two: Build DNA depth object
python /scratch/ps163/Carolina_03_18_2021/abaixe/abaixe.py -depth \
    -experiment /scratch/ps163/Carolina_03_18_2021/pickles/cnv_experiment.p \
    -filter /scratch/ps163/Carolina_03_18_2021/metadata/filter_regions.tab
    
# Step three: normalize read depth
python /scratch/ps163/Carolina_03_18_2021/abaixe/abaixe.py \
    -norm \
    -experiment /scratch/ps163/Carolina_03_18_2021/pickles/cnv_experiment.p \
    -o pickles/ 

# Step four: calculate DNA depth for a region
python /scratch/ps163/Carolina_03_18_2021/abaixe/abaixe.py -locus_depth \
    --gene_systematic_name_file /scratch/ps163/Carolina_03_18_2021/metadata/FeatureType_Gene_SGD_Systamtic_Standard.tsv \
    -gff /scratch/ps163/Carolina_03_18_2021/ensembl_50/Saccharomyces_cerevisiae.R64-1-1.50.gff3 \
    -feature ensembl \
    -experiment pickles/cnv_experiment.p -o pickles

# Step five: calculate expression over a region 
python /scratch/ps163/Carolina_03_18_2021/abaixe/abaixe.py -locus_expression \
    --gene_systematic_name_file /scratch/ps163/Carolina_03_18_2021/metadata/metadata/FeatureType_Gene_SGD_Systamtic_Standard.tsv \
    -gff /scratch/ps163/Carolina_03_18_2021/ensembl_50/Saccharomyces_cerevisiae.R64-1-1.50.gff3 \
    -feature ensembl \
    -experiment pickles/cnv_experiment.p -o pickles/ror
    
# Step six: adjust expression over a region to account for DNA depth
python abaixe.py -adjust_expression \
    -experiment pickles/cnv_experiment.p -o pickles/ror    

# Step seven: evaluate abundance changes in expression 
python abaixe.py -expression -experiment pickles/cnv_experiment.p \
    -experiment pickles/cnv_experiment.p \
    -o ror_exp_differences
    
# Step eight: evaluate positional changes in expression
python /scratch/ps163/Carolina_03_18_2021/scripts/abaixe.py --positional_regions \
    -experiment /scratch/ps163/Carolina_03_18_2021/pickles/cnv_experiment.p \
    -gff /scratch/ps163/Carolina_03_18_2021/ensembl_50/Saccharomyces_cerevisiae.R64-1-1.50.gff3 \
    -feature ensembl \
    -o cnv_exp
