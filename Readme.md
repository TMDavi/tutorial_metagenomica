# Metagenomics Tutorial
## Taxonomic analysis
### Kraken basic command

        #!/bin/bash
        directory='/path/to/file'
        results='path'

        for file in ${directory}/*R1.fastq.gz
            do
            prefix=`basename $file R1.fastq.gz`
            kraken2 --db /MP_Data/database/kraken2/last_release --paired $file $directory/${prefix}R2.fastq.gz --report ${results}/${prefix}report.txt --threads 30
            done

#### Convert kreport to mpa using Kraken Tools
- available at https://github.com/jenniferlu717/KrakenTools.git

        #!/bin/bash
    
        krakentools='path/to/krakentools'
        directory='/path/to/kreports'
        
        for file in ${directory}/*report.txt
            do
            prefix=`basename $file txt`
            python ${krakentools}/kreport2mpa.py -r ${file} -o ${prefix}mpa.txt 
            done
#### Covert mpa to the MicrobiomeAnalyst table
- use the script kraken2micro available at https://github.com/labgm/kraken2micro.git

        python kraken2micro.py --rank 'S' --organism 'Bacteria' --files file1_mpa.txt file2_mpa.txt file3_mpa.txt ...
#### Example metadata file microbiome analyst
        #NAME SampleType
        sample_01_report.mpa	case
        sample_02_report.mpa	case
        sample_03_report.mpa	case
        sample_04_report.mpa	control
        sample_05_report.mpa	control
        sample_06_report.mpa	control

## Resistance analysis

### Megares

        #!/bin/bash
        directory='path/to/directory'
        results='path/to/results'
        
        for sample in ${directory}/*_1.fq.gz
        do
            name=`basename ${sample} _1.fq.gz`
            nextflow run /usr/local/AMRplusplus/main_AMR++.nf -profile local --pipeline resistome --reads "${directory}/${name}_{1,2}.fq.gz" --output "${results}/${name}" --threads 40 
        done
### CARD RGI
1) Ativar ambiente RGI 

        conda activate rgi
2) Rodar o script do CARD

        #!/bin/bash
        directory='path/to/directory'
        results='path/to/results'
        
        for sample in ${directory}/*_1.fq.gz
        do 
            name=`basename ${sample} _1.fastq.gz`

            rgi bwt --read_one ${directory}/${name}_1.fq.gz --read_two ${directory}/${name}_2.fq.gz --output_file ${results}/${name} --local -n 60 --include_wildcard --include_other_models
        done

3) Remover arquivos temporários dentro da pasta de resultados

        rm *.seqs.temp.txt
        rm *.sorted.*.bam
        rm *.temp.bam
        rm *.temp.sam
        rm *.gz
        rm *.temp.sam.temp*
        rm *.coverage.temp.txt
        rm *.model_species_data_type.temp.txt
        rm *.sorted.*.bam.bai
        rm *.coverage_all_positions.temp.txt
        rm *.allele_mapping_data.json
        rm *.temp.txt

4) Posteriormente deve-se utilizar as tabelas de gene_mapping ou de allele mapping e filtrados os genes com pelo menos 70% de cobertura

## Anotação Funcional

1) Ativar ambiente do eggnog

        conda activate eggnog
2) Rodar o script
        
        emapper.py --data_dir /MP_Data/database/eggnog/ --cpu 40 --itype metagenome --genepred prodigal -i sample.fa -o Results_dir
- Só funciona com os contigs ou seja é necessário etapa de montagem anteriormente

## Montagem megahit

        megahit -f -1 {input[0]} -2 {input[1]} -t {threads} --presets meta-large -o {params.output} --min-contig-len 300

















