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
  
  Messed some stuff up pretty bad while trying to fix files with improper headers following sorting and replacing "unknown." Reset back to this point. Had just made a new directory in Unix_Assignment called processed_data. Copied over joined_maize.txt and joined_teosinte.txt from data directory and took headers from the aforementioned files and added them to new files.
  
    mkdir processed_data
    
    cp joined* ~/Unix_Assignment/processed_data
    
    head -n1 joined_maize.txt | tee > asc_joined_maize.txt > des_joined_maize.txt
    head -n1 joined_teosinte.txt | tee > asc_joined_teosinte.txt > des_joined_teosinte.txt

  Created joined_maize/teosinte.txt files without headers for sorting without the header getting in the way
    
    sed '1d' joined_maize.txt > nohead_joined_maize.txt
    sed '1d' joined_teosinte.txt > nohead_joined_teosinte.txt

  For the ascending files, first used sed to replace "unknown" with "?". Next, sorted first alphanumerically within the string (-V) on the chromosome column, then numerically on the position column. Then append to file with the appropriate header. Did the same for the descending files, but replaced "unknown" with "-" then and sorted the position column recursively as well.
  
    sed 's/unknown/?/g' nohead_joined_maize.txt | sort -k2,2V -k3,3n >> asc_joined_maize.txt
    sed 's/unknown/?/g' nohead_joined_teosinte.txt | sort -k2,2V -k3,3n >> asc_joined_teosinte.txt
  
    sed 's/unknown/-/g' nohead_joined_maize.txt | sort -k2,2V -k3,3nr >> des_joined_maize.txt 
    sed 's/unknown/-/g' nohead_joined_teosinte.txt | sort -k2,2V -k3,3nr >> des_joined_teosinte.txt 

  Copied the four files created in the step above to a new directory named files. Created a script to separate the four files by chromosome.
  
    #on a file with a sorted column, generates separate files: one file for each unique value in the sorted
    #column, with all rows bearing that unique value. Header of source file is given to all new files.

    #useage: bash split_file filename column_to_sort what_to_name_new_files

    column=$2                                                                               #sets input column_to_sort value as the varia$

    for file in $1                                                                          #sets input filename as the variable "file"
    do
        awk 'FNR > 1 {print $'$column'}' $file | uniq > temp.txt                        #skip the first line of the file, pick the "c$

        while read line_temp; do                                                        #go line by line through temp file

                head -n1 $file > "$line_temp"_"$3".txt                                  #take header of source file and output it to $

                while read line_file; do                                                #go line by line through file

                        awk -v var=$line_temp '$'$column' == var\
                        && $3 != "multiple"' >> "$line_temp"_"$3".txt                   #set current line of temp file as variable, t$
                                                                                        #and whose third field is not equal to "multi$

                done <$file

        done <temp.txt

    rm temp.txt                                                                             #remove temp file to keep things clean

    done
    
  Ran the script, generating the files required for the assignment. Running a script on all four source files (asc_joined_maize/teosinte.txt and des_joined_maize/teosinte.txt) created two files of the unknown data (? or -) and two files of the multiple data for both the ascending and descending source files. 
  
  While looking over files, realized that "unknown" in des_joined_maize.txt had been replaced by a "?" rather than a "-" like it should have been (ignore the commit message, it has it written the other way around). Also removed unnecessary files.
  
  Realized that none of the unknowns should've been replaced. Should have replaced ?/? instead. Changed sed commands.
  
    sed 's:?/?:?:g' nohead_joined_maize.txt | sort -k2,2V -k3,3n >> asc_joined_maize.txt
    sed 's:?/?:?:g' nohead_joined_teosinte.txt | sort -k2,2V -k3,3n >> asc_joined_teosinte.txt

    sed 's:?/?:-:g' nohead_joined_maize.txt | sort -k2,2V -k3,3nr >> des_joined_maize.txt
    sed 's:?/?:-:g' nohead_joined_teosinte.txt | sort -k2,2V -k3,3nr >> des_joined_teosinte.txt





    
    
    
    
    
    
    
    
    



  
    
  
  
