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
