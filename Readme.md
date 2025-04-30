# Metagenomics Tutorial
## Taxonomic analysis
### Kraken basic command

        #!/bin/bash
        directory='/path/to/file'

        for file in ${directory}/*R1.fastq.gz
            do
            prefix=`basename $file R1.fastq.gz`
            kraken2 --db /MP_Data/database/kraken2/last_release --paired $file $directory/${prefix}R2.fastq.gz --report /MP_Data/rommel/sectet/raw/maraba/kraken_test/${prefix}report.txt --use-mpa-style --threads 30
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

## Resistance analysis

### Megares

        #!/bin/bash
        directory='path/to/directory'
        results='path/to/results'
        
        for sample in ${directory}/*_1.fq.gz
        do
            name=`basename ${sample} _1.fq.gz`nextflow run /usr/local/AMRplusplus/main_AMR++.nf -profile local --pipeline resistome --reads "${directory}/${name}_{1,2}.fq.gz" --output "${results}/${name}" --threads 40 
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

        rm *_rgi.seqs.temp.txt
        rm *_rgi.sorted.*.bam
        rm *_rgi.temp.bam
        rm *_rgi.temp.sam
        rm *.gz
        rm *_rgi.temp.sam.temp*
        rm *_rgi.coverage.temp.txt
        rm *_rgi.model_species_data_type.temp.txt
        rm *_rgi.sorted.*.bam.bai
        rm *_rgi.coverage_all_positions.temp.txt
        rm *_rgi.allele_mapping_data.json
        rm *_rgi.temp.txt

4) Posteriormente deve-se utilizar as tabelas de gene_mapping ou de allele mapping e filtrados os genes com pelo menos 70% de cobertura

## Anotação Funcional

1) Ativar ambiente do eggnog

        conda activate eggnog
2) Rodar o script
        
        emapper.py --data_dir /MP_Data/database/eggnog/ --cpu 40 --itype metagenome --genepred prodigal -i sample.fa -o Results_dir
- Só funciona com os contigs ou seja é necessário etapa de montagem anteriormente


















