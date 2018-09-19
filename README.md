# BCB54X_Unix_Assignment

Assignment goal is to analyze data files and prepare them for processing

Analysis
  
  Created bash script "data_inspection"
  
   Will add code snipet later, currently can't install Markdown because IT won't grant me admin privileges on my work computer
    
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

  Deleted Unix Assignment direction files from data folder earlier. Also deleted transpose.awk. Oops.
  
  Used nano to create transpose.awk and added in the script from https://github.com/EEOB-BioData/BCB546X-Fall2018/blob/master/assignments/UNIX_Assignment/transpose.awk
  
  Used transpose.awk to transpose fang_et_al_genotypex.txt into transposed_genotypes.txt
       awk -f transpose.awk fang_et_al_genotypes.txt > transposed_genotypes.txt


