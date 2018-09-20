# BCB54X_Unix_Assignment

Assignment goal is to analyze data files and prepare them for processing

Analysis
  
  Created bash script "data_inspection"
  
    #inspects file, returning size, lines, characters and content, and columns
    #useage: bash data_inspection filename

    for var in "$1"                                             #takes field 1 (filename) and assigns it to var
    do
        echo -e "File size:\t\t" $(du -h $var | cut -f1)        #du -h: prints disk useage of file; cut -f1: print number only
        echo -e "Total lines:\t\t" $(wc -l < $var)              #prints total number of lines in file; < : print number only
        echo -e "Total characters:\t" $(wc -m < $var)           #prints total number of characters in file;  < : print number only
        echo -e "Character content:\t" $(file $var)             #prints character content
        echo -e "Columns in first line:\t"\
                $(awk -F "\t" '{print NF; exit}' $var)          #prints number of columns in first line

     done

   In the terminal, executing "bash data_inspection 'filename'" will run the script on the given file
   
   Prints file size, total lines, total characters and character content, and number of columns in first line 
  
  Using data_inspection, the following data was obtained:

    fang_et_al_genotypes.txt
      File size:               11M
      Total lines:             2783
      Total characters:        11051939
      Character content:       fang_et_al_genotypes.txt: ASCII text, with very long lines
      Columns in first line:   986
  
    snp_position.txt
      File size:               84K
      Total lines:             984
      Total characters:        82763
      Character content:       snp_position.txt: ASCII text
      Columns in first line:   15

Preparation for processing

  Deleted Unix Assignment direction files from data folder earlier. Also deleted transpose.awk. Oops. Used nano to create transpose.awk and added in the script from https://github.com/EEOB-BioData/BCB546X-Fall2018/blob/master/assignments/UNIX_Assignment/transpose.awk
  
  Used transpose.awk to transpose fang_et_al_genotypex.txt into transposed_genotypes.txt
  
       awk -f transpose.awk fang_et_al_genotypes.txt > transposed_genotypes.txt

  Rows with data applicable to maize are denoted by ZMMIL, ZMMLR, and ZMMMR in the third column of the fang_et_al_genotypes.txt file. Rows with data applicable to teosinte are denoted by ZMPBA, ZMPIL, and ZMPJA in the third column of the fang_et_al_genotypes.txt file. Used grep to isolate maize and teosinte data and give them their own files.
  
    grep grep ZMM fang_et_al_genotypes.txt > maize_genotypes.txt
    grep ZMP fang_et_al_genotypes.txt > teosinte_genotypes.txt

  Used transpose.awk to transpose these files
        
    awk -f transpose.awk maize_genotypes.txt > transposed_maize_genotypes.txt
    awk -f transpose.awk teosinte_genotypes.txt > transposed_teosinte_genotypes.txt
    
  Cut out necessary (SNP ID, Chromosome, Position) columns from snp_position.txt
  
    cut -f 1,3,4 snp_position.txt > snp_position_relevant.txt
    
  Inspected snp_position_relevant.txt and the transposed_maize/teosinte_genotype.txt files using head and wc-l. Found that the transposed_maize/teosintegenotype.txt files had two more lines at the beginning of the file (986 total) compared to the snp_position_relevant.txt file. Removed those lines with sed.
  
    sed '2d ; 3d' transposed_maize_genotypes.txt > 1transposed_maize_genotypes.txt
    sed '2d ; 3d' transposed_teosinte_genotypes.txt > 1transposed_teosinte_genotypes.txt
  
  Attempted to join the 1transposed_maize/teosinte_genotypes.txt files with the snp_position_relevant file. Failed due to an error saying that one of the fields was "not in sorted order." Tried sorting, then joining, but that failed. Then realized that the column 1 header of the 1transposed files was "Sample_ID" while the column 1 header of the snp_position_relevant file was "SNP_ID." Used sed to change "Sample_ID" to "SNP_ID."
  
    sed 's/Sample_ID/SNP_ID/g' 1transposed_maize_genotypes.txt > 2transposed_maize_genotypes.txt
    sed 's/Sample_ID/SNP_ID/g' 1transposed_teosinte_genotypes.txt > 2transposed_teosinte_genotypes.txt
    
  Join then worked correctly
  
    join -1 1 -2 1 snp_position_relevant.txt 2transposed_maize_genotypes.txt > joined_maize.txt
    join -1 1 -2 1 snp_position_relevant.txt 2transposed_teosinte_genotypes.txt > joined_teosinte.txt

  Deleted transposed_maize/teosinte and 1transposed_maize/teosinte files. Renamed 2transposed_maize/teosinte_genotype.txt files transposed_maize/teosinte_genotype_edi.txt using the mv command
  
    mv 2transposed_maize_genotypes.txt transposed_maize_genotypes_edited.txt
    mv 2transposed_teosinte_genotypes.txt transposed_teosinte_genotypes_edited.txt
    
  Tried to git add . afterwards, but it wouldn't allow it unless I added --all or --ignore-removal. Used --ignore-removal. Files deleted are still absent from directory, but they are present on Github. Not sure if that's due to the --ignore-removal command (I think it holds onto removed files, but I'm not sure)
  
  


    
    
    
    
    
    
    
    
    



  
    
  
  
