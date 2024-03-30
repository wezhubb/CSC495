<span style="font-family: 'New Times', serif; font-size: 15px;">

# Title: Development of an Automated Medication Name Correction Algorithm

**Student:** Wenzhu Ye

**Supervisors:** Dr. Clement Zai and Dr. Graeme Hirst

**Date:** Mar 27. 2024

***
# Introduction

&nbsp;&nbsp;&nbsp;&nbsp; In the current database for the IMPACT pharmacogenetics study at the Centre for Addiction and Mental Health (CAMH), the research analysts manually recorded prescriptions for research patients. However, the manual entry of medication names resulted in errors, such as misspellings and incorrect formats. These errors could complicate future data interpretation, potentially compromising our ability for data analyses.  From an initial survey of the medications that were entered into the IMPACT database, there were approximately 10,000 misspelt medication names, and while most of these errors were easily recognizable by humans, manually correcting them would be time-consuming, inefficient, and could potentially lead to additional errors. In order to mitigate this challenge and ensure the accuracy and reliability of medication data within the IMPACT database, it is imperative to develop an auto-correction algorithm that would correct existing misspelt medication names. This algorithm would significantly reduce the time and effort required for manual correction. Moreover, we would be able to trace back to the original entries if any additional errors were found in the future.  Further, setup for the auto-correction databank that will be used by the auto-correction algorithm would only require medication information without personal health or identifying information of the research participants, which protects sensitive information. 

&nbsp;&nbsp;&nbsp;&nbsp; To summarize our current progress in solving this problem accomplished in the previous course CSC494, we employed a standard medication word bank as a reference for correcting misspelt words. This word bank was derived from the psychiatric drug bank within CAMH, and a general bank sourced from Drug Bank. For the misspelt prescription, we break down each medication into individual words using the Natural Language Toolkit (NLTK) package (Bird et al, 2009). Then for each of the individual words, we compared each word against each standard medication in the medication bank to identify and correct misspelt entries.

&nbsp;&nbsp;&nbsp;&nbsp; The method used to quantify the similarity between two words is the ratio method from the FuzzyWuzzy package (Mouselimis 2021). The method used edit distance (also known as Levenshtein distance) as the fundamental concept, which is a measure of the minimum number of single-character edits required to transform one string into another (Levenshtein, 1965). The algorithm's performance using edit distance is optimal at a threshold of around 80, achieving an accuracy rate of 72.5%. We also plot the ROC curve and calculate AUC, both results support the optimal threshold with a balanced trade-off between false positive and true positive rate. 

&nbsp;&nbsp;&nbsp;&nbsp; Although we had achieved an accuracy of 72.5%, we still question whether it would be possible to achieve a better accuracy.

&nbsp;&nbsp;&nbsp;&nbsp; One possible solution is to incorporate a phonetic algorithm. Currently, the methodology solely relies on string distance, neglecting the fact that people often spell words based on the word’s phonetic sound. For example, "Smith" and "Smythe" would be spelt differently but pronounced similarly. To address this limitation, the next phase of our research will incorporate a phonetic-based algorithm, specifically Soundex and Metaphone, to enhance the accuracy of medication name corrections.

&nbsp;&nbsp;&nbsp;&nbsp; Soundex is a rule-based phonetic algorithm used to encode words on their English pronunciation. Soundex will assign a four-digit code for each word, a letter followed by three numbers, representing the word’s phonetic sound. Words that have similar pronunciation will receive the same or similar Soundex codes (Russell and Odell, 1918). In the previous example, "Smith" and "Smythe” would have the same code of S530. Besides Soundex, another rule-based phonetic algorithm we would apply is Metaphone. Like Soundex, Metaphone generates a phonetic representation of a word based on its sound. However, the phonetic representation Metaphone generated is not limited to the length of four but varies depending on the pronunciation of the word. The Metaphone algorithm is an improvement on Soundex and includes the consideration of common prefixes, vowels, consonants, and silent letters (Lawrence 1990). In the previous example, "Smith" and "Smythe” would have the same code of SM0.	

&nbsp;&nbsp;&nbsp;&nbsp; In the pursuit of refining our auto-correction algorithm, our objective is to choose the method that yields the highest accuracy rate for autocorrection out of all possible methods: fuzzy, Soundex, Metaphone, and a combination of phonetic with fuzzy matching techniques. This integration aims to produce an algorithm capable of correcting misspelt medication names with a higher level of accuracy. By exploring the synergy between phonetic and string-based approaches, we anticipate achieving a more comprehensive solution to the challenges posed by medication name misspellings in the IMPACT database.

# Method

## Coding background and environment
&nbsp;&nbsp;&nbsp;&nbsp; The program will be developed using Python 3.11 within the PyCharm Integrated Development Environment (IDE).

## Medication bank and data preparation
&nbsp;&nbsp;&nbsp;&nbsp; We will use the same psychiatric bank and the general bank prepared in CSC494. Similarly, the misspelt data is already extracted and cleaned from CSC494 and separated into three parts: 200 words of development data to fine-tune parameters, 200 words of testing data to evaluate the program’s accuracy at the end of the project, and all remaining data as the actual data to perform the algorithm. We will continue to work with the same set of data.

## Phonetic Algorithm Viability
&nbsp;&nbsp;&nbsp;&nbsp; We will test whether the Soundex and Metaphone algorithm suits this project by examining how many medication names in medication banks have the same Soundex/Metaphone code. If the number of medications that have the same Soundex/Metaphone code is large, then we will consider applying another phonetic method.

## Correction use Phonetic algorithm only
&nbsp;&nbsp;&nbsp;&nbsp; We will implement the phonetic algorithm from the Python Jellyfish package [version 1.0.3] using the Soundex method and Metaphone method respectively. For phonetic algorithms, we will get a unique phonetic representation code for each word. This code is then compared to the codes of each word in the standard bank for both common and generic medication names. A match is identified only if the word pairs share the same code generated by either of the two algorithms. In cases where more than one standard word is paired with a misspelt word, a human decision-making step is introduced to resolve potential ambiguities. Notably, if a word fails to find a match with any word in the standard bank, it remains uncorrected.

## Correction use Phonetic combine with fuzz
&nbsp;&nbsp;&nbsp;&nbsp; Each tokenized word will undergo a combination of the phonetic algorithm and Fuzz methods to assess the agreement between the two algorithms. We will examine the performance of the tokenized word pass either of the two algorithms. Firstly, we would examine whether there is a match using a phonetic algorithm. If there is a match, then we would not look at the fuzz algorithm and pair the tokenized word and standard word directly. However, if there is not a match, then we would use the fuzz method to find a match. If there is not a match for both methods, then the would will remain uncorrected.
<!-- We will first examine the performance of whether the tokenized word pass both phonetic and fuzz method.
A match will be identified if both the Fuzz Method and the phonetic Method agree, meaning the Fuzz score surpasses a predefined threshold, and the phonetic code output indicates a match with the corresponding standard medication name. <span style="color: red;">check algo details</span> -->
<!-- In cases where the algorithms exhibit disagreement but one of them produces a high score—either the Fuzz score surpasses the threshold, or the Soundex/Metaphone output indicates a match—human intervention becomes imperative for a manual decision. If the algorithms do not agrees, and both scores are low, the input word pair is categorized as not matched.  -->

<!-- 
## Testing
To evaluate the program's performance, a thorough testing phase will be conducted. Corrections generated by our algorithms were compared against human-provided answers for each corrected phrase. A match between the two indicated a correct correction, while discrepancies classified the correction as incorrect. The testing program will output the total number of correct and incorrect corrections for each algorithm.  <span style="color: red;">add more on testing details</span> -->

# Coding and Result

## Load standard medication bank
&nbsp;&nbsp;&nbsp;&nbsp; The first step to developing the algorithm would be to load in the standard medication bank. This bank serves as the basis of our algorithm.
```
def load_bank() -> list:
    # path to the data
    xls = pd.ExcelFile('../standard.xlsx')
    psy = pd.read_excel(xls, sheet_name='psy', header=None)
    gen = pd.read_excel(xls, sheet_name='all_edit', header=None)

    # read in general bank
    gen_lst = []
    for i in gen.index:
        common = gen[0][i].split('/')
        gen_lst.append(common)

    # read in psychatric bank
    psy_lst = []
    for i in psy.index:
        common = psy[0][i].strip().split(' ')
        common[-1] = common[-1][1:-1]
        psy_lst.append(common)
    
    # return both bank in a list of list, were general bank is at index 0 and psychatric bank at index 1
    return [gen_lst, psy_lst]

```
&nbsp;&nbsp;&nbsp;&nbsp; The code snippet above defined a function load_bank(), where the function returns our medication bank.

&nbsp;&nbsp;&nbsp;&nbsp; We can view the a sample output of our medication bank:
```
>>> result = load_bank()
>>> psy_bank = result[1] # psychatric bank will be stored as psy_bank
>>> gen_bank = result[0] # general bank will be stored as gen_bank

# print the first ten medication of the psychatric bank
>>> print(psy_bank[:10])
[['amitriptyline', 'Elavil'], ['aripiprazole', 'Abilify'], ['asenapine', 'Saphris'], ['bupropion', 'Wellbutrin'], ['chlorpromazine', 'Largactil'], ['citalopram', 'Celexa'], ['clomipramine', 'Anafranil'], ['clozapine', 'Clozaril'], ['desipramine', 'Norpramin'], ['desvenlafaxine', 'Pristiq']]

# print the first ten medication of the general bank
>>> print(gen_bank[:10])
[['Penicillin'], ['accolate', 'zafirlukast'], ['zytram'], ['accuneb', 'albuterol', 'vospire'], ['accuretic', 'quinapril+hydrochlorothiazide'], ['aceon', 'perindopril'], ['aclovate', 'alclometasone'], ['actigall', 'ursodiol', 'urso'], ['actiq', 'fentanyl'], ['adalat', 'nifedipine']]

```
&nbsp;&nbsp;&nbsp;&nbsp; From the above sample output, we can see our medication bank is in the data format of a list of lists, where each outer list represents a single medication, and the inner list represents all the common names for that medication.

## Check Phonetic Algorithm Viability
```
# create two dictionary where the key is the phonetic code, and the value is list of medication that match to that phonetic code.
soundex_to_med = {}
meta_to_med = {}

# loop through every instance of standard bank
for item in gen_bank:
    for med in item:
        # get the phonetic code of both method
        sound = jellyfish.soundex(med)
        meta = jellyfish.metaphone(med)

        # add soundex code - medication pair to dictionary
        if sound not in soundex_to_med:
            soundex_to_med[sound] = med
        else:
            soundex_to_med[sound].append(med)

        # add metaphone code - medication pair to dictionary
        if meta not in meta_to_med:
            meta_to_med[meta] = med
        else:
            meta_to_med[meta].append(med)
```
&nbsp;&nbsp;&nbsp;&nbsp; Now the soundex_to_med and meta_to_med dictionary contain each phonetic code mapped to a list of medications that match that code. If any code matches a list that has a length bigger than one, indicate on phonetic code that matches multiple medications, then that would be a crush. Thus, we count the total number of keys for each of the two dictionaries that have a list length bigger than one.

```
crush_soundex = 0
crush_meta = 0

for key in soundex_to_med:
    # if the length > 1, we increase count by 1
    if len(soundex_to_med[key]) > 1: 
        crush_soundex += 1

for key in meta_to_med:
    # if the length > 1, we increase count by 1
    if len(meta_to_med[key]) > 1:
        crush_meta += 1

print(crush_soundex) # the resut we got is 2282
print(crush_meta) # the resut we got is 494
```
&nbsp;&nbsp;&nbsp;&nbsp; From the result, we can see the metaphone method has less crush (494) than the soudex method (2282). Thus, we would be using the phonetic method Metaphone in the remaining of the algorithm.

## Matching word by phonetic algorithm 
&nbsp;&nbsp;&nbsp;&nbsp; The next step will be to develop functions to match two words by phonetic (Metaphone) code. 

### Develop phonetic code bank
&nbsp;&nbsp;&nbsp;&nbsp; The first step would be to develop a metaphone code bank for each of the words in the standard medication bank. The reason for developing this bank is that we could easily access the standard medication with its phonetic code. For example, we have misspelt words with the phone code **MLPG**. If we want to find a match with this misspelt word in the standard bank, we just need to search for the code **MLPG** in the standard bank.

```
def load_phonetic_bank(psy_bank, gen_bank) -> Dict:
    meta_to_med = {}

    # generate phonetic bank for psychatric bank
    for item in psy_bank:
        for med in item:
            # get metaphone code
            meta = jellyfish.metaphone(med)

            if meta not in meta_to_med:
                meta_to_med[meta] = {med: item}
            else:
                meta_to_med[meta][med] = item

    # generate phonetic bank for psychatric bank
    for item in gen_bank:
        for med in item:
            meta = jellyfish.metaphone(med)

            if meta not in meta_to_med:
                meta_to_med[meta] = {med: item}
            else:
                meta_to_med[meta][med] = item

    return meta_to_med
```
&nbsp;&nbsp;&nbsp;&nbsp; We can view a sample output of the phonetic bank.
```
>>> phonetic_bank = load_phonetic_bank(psy_bank, gen_bank)
>>> print(dict(list(phonetic_bank.items())[:5])) # print first 5 items
{'AMTRPTLN': {'amitriptyline': ['amitriptyline', 'amitriptilina', 'amitriptylin', 'amitriptylinum']}, 'ELFL': {'Elavil': ['amitriptyline', 'Elavil']}, 'ARPPRSL': {'aripiprazole': ['aripiprazole', 'aripiprazol', 'aripiprazolum']}, 'ABLF': {'Abilify': ['aripiprazole', 'Abilify']}, 'ASNPN': {'asenapine': ['asenapine', 'asenapina']}}

```
&nbsp;&nbsp;&nbsp;&nbsp; From the above output, we can see that our phonetic bank is in the data format of {phonetic code: {medication name: [list of common name]}}, where the key for the outer dictionary is the phonetic code, and the value is another dictionary. The key for the inner dictionary is the medication name corresponding to that phonetic code, and the value is a list of the medication's common name.

### Match misspelt word to standard medication bank using phonetic algorithm only
&nbsp;&nbsp;&nbsp;&nbsp; With the phonetic bank, we can now develop a function matching the misspelt word to standard medication.

```
def word_process_meta(misspelled: str, bank: dict) -> str:
    # tokenize the phrase into single word
    tokens = nltk.word_tokenize(misspelled)

    # initalize result
    # the key is each tokenized word 
    # the value is the final matched standard word
    # if the tokenized word have no match, the value will be an empty string
    result = {}
    for item in tokens:
        result[item] = ''

    # loop through all the tokenzied word
    for i in range(len(tokens)):
        # get metaphone code for that word
        meta_code = jellyfish.metaphone(tokens[i])

        lst = []
        if meta_code in bank:
            for key in bank[meta_code]:
                # change the format of list of common name into string, where each common name is seperated by '/'
                lst.append('/'.join(bank[meta_code][key]))

            if len(lst) > 1:
                # ignore any tokenized word that length smaller than 4
                if len(tokens[i]) < 4:
                    continue

                # ignore any tokenized word that is all digit
                if tokens[i].isdigit():
                    continue

                # if there are more than one standard medication name with the same phonetic code, a human needed to make the decision
                dec = decision(tokens[i], lst)

                # human decision
                if dec != -9 and dec != -1:
                    result[tokens[i]] = dec

            else:
                result[tokens[i]] = lst[0]

    # wrap all matched word with square bracket so they can be easily recognized
    for i in range(len(tokens)):
        if result[tokens[i]] != '':
            tokens[i] = '[' + result[tokens[i]] + ']'
    return ' '.join(tokens)
```
&nbsp;&nbsp;&nbsp;&nbsp; An example of using this function:
```
>>> phrase = 'I was on Zoloft'
>>> print(word_process_meta(phrase, phonetic_bank))
'I was on [sertraline/Zoloft]' 
```
&nbsp;&nbsp;&nbsp;&nbsp; From the above example, we can see that 'Zoloft' is caught by our algorithm, and matched to 'Zoloft' in the phonetic bank. The function then changes the original 'Zoloft' to '[sertraline/Zoloft]', which contains Zoloft's common name, and the corrected word is surrounded by a square bracket, thus, making it easy to identify.

### Match misspelt word to standard medication bank combination of fuzz and phonetic
&nbsp;&nbsp;&nbsp;&nbsp; We will now develop a function that uses a combination of fuzz and phonetic method to match words. For fuzz, we will be using the optimal threshold of 80.

```
# NOTICE: the function word_process() is developed in CSC494, that uses only fuzz to match word

def word_process_combine(misspelled: str, phonetic_bank: dict, psy_bank: list, gen_bank: list, threshold: int) -> str:
    # initalize tokens
    # more detail please view word_process_meta()
    tokens = nltk.word_tokenize(misspelled)
    result = {}

    for item in tokens:
        result[item] = ''

    for word in tokens:
        meta_code = jellyfish.metaphone(word)
    
        # if metaphone code in phonetic bank, we will use metaphone
        if meta_code in phonetic_bank:
            result[word] = word_process_meta(word, bank)

        # if there is no metaphone code in phonetic bank, we will use fuzz
        else:
            result[word] = word_process(word, psy_bank, gen_bank, threshold)

    # wrap all matched word with square bracket so they can be easily recognized   
    for i in range(len(tokens)):
        if result[tokens[i]] != '':
            tokens[i] = '[' + result[tokens[i]] + ']'
    return ' '.join(tokens)
```
&nbsp;&nbsp;&nbsp;&nbsp; This function outputs the same data format as word_process_meta(). The function first checker whether there is a match using the phonetic method, if there is no match, then it will check whether there is a match using the fuzz method. 

## Correction


### Correction use Phonetic algorithm only
 &nbsp;&nbsp;&nbsp;&nbsp; We will first perform out alogrithm with phonetric algorithm only.
 ```
 # NOTICE: From csc494
 # All the raw misspelled data store in variable misspelled_data. 
 # All development data store in variable develop_data. 

 # pair is a dictionary, where the key is the misspelled phrase and value is the corrected phrase.
pair = {}
for item in develop_data:
    pair[item] = word_process_meta(item, phonetic_bank)

 ```
 &nbsp;&nbsp;&nbsp;&nbsp; Now dictionary pair has stored all the corrected information. We then compared the algorithm output with human-provided output, and we got:
 ```
 Total number of word: 200,
Number of correct: 115,
Number of incorrect: 85,
Accuracy: 0.575,
False Positive: 92,
False Negative: 47,
True Positive: 137,
True Negative: 451,
 ```

### Correction use both algorithm
 &nbsp;&nbsp;&nbsp;&nbsp; We will first perform out alogrithm with both methods.
 ```
pair = {}
for item in develop_data:
    pair[item] = word_process_combine(item, phonetic_bank)
 ```
 &nbsp;&nbsp;&nbsp;&nbsp; The output we got:
 ```
Total number of word: 200,
Number of correct: 128,
Number of incorrect: 72,
Accuracy: 0.64,
False Positive: 115,
False Negative: 19,
True Positive: 171,
True Negative: 425,
 ```

### Identify problem
&nbsp;&nbsp;&nbsp;&nbsp; From the above output, we can see that both accuracies are low. 
&nbsp;&nbsp;&nbsp;&nbsp; Looking into the incorrect output, we identified situations like 
```
Misspelled Word: 'None now'; 
correction using program: '[neon/ne/neón/néon] now'; 
correct correction: 'None now' 

Misspelled Word: 'Many of my medications are split into two doses'; 
correction using program: 'Many of my medications are split [indium/indio] two doses'; 
correct correction: 'Many of my medications are split into two doses' 
```
&nbsp;&nbsp;&nbsp;&nbsp; We can see that in lots of situations, a common general word like 'none' and 'into' are correct, which causes us to have a high false positive rate. To solve this problem, we decided to incorporate a general word bank, in which if there is any word in the general word bank, we stop trying to correct them.

## General word bank
&nbsp;&nbsp;&nbsp;&nbsp; We get the general word bank from BNC/COCA word family lists (Nation 2017), which it composed of the 10,000 most common words in English, and do not contain any medication name, which avoids the possible risk of increased false negative rate.

&nbsp;&nbsp;&nbsp;&nbsp; We load in the general word bank
```
def load_gen_word():
    lst = []
    # loop through all the files
    for file in os.listdir('10000-headwords'):
        # get filename and open
        filename = os.fsdecode(file)
        f = open('10000-headwords/' + filename, 'r')

        # read in each of the word
        line = f.readline().strip()
            # iterate until reach the end of the document
            while line != '':
                # convert all the word into upper cases
                lst.append(line.upper())
                line = f.readline().strip()
    return lst
```
&nbsp;&nbsp;&nbsp;&nbsp; To view sample output:
```
>>> gen_word = load_gen_word()
>>> print(gen_word[:10])
['ABBEY', 'ABDOMEN', 'ABOLISH', 'ACCESSORY', 'ADORE', 'ADVOCACY', 'AERIAL', 'AFTERMATH', 'AGGRAVATE', 'AGGREGATE']
```
&nbsp;&nbsp;&nbsp;&nbsp; Thus, we now have all the 10000 store into the list gen_word.

## Correction with general word bank
With the general word bank, we now need to incorporate this bank into our code, where we want to ignore to correct any word if that word is in the general word bank. In order to accomplish this, we can simply add if condition:
```
for word in tokens:
    # since all the word in gen_word is in upper case, we need to conver word into upper cases as well
    if word.upper() in gen_word:
        continue
```

### Correction use Phonetic algorithm only
&nbsp;&nbsp;&nbsp;&nbsp; We now re-run previous code, and we get
```
Total number of word: 200,
Number of correct: 134,
Number of incorrect: 66,
Accuracy: 0.67,
False Positive: 45,
False Negative: 49,
True Positive: 139,
True Negative: 490,
```
### Correction use fuzz algorithm only
&nbsp;&nbsp;&nbsp;&nbsp; Using fuzz algorithm only, we get
```
Total number of word: 200,
Number of correct: 152,
Number of incorrect: 48,
Accuracy: 0.76,
False Positive: 57,
False Negative: 15,
True Positive: 175,
True Negative: 480,
```
### Correction use both algorithm
&nbsp;&nbsp;&nbsp;&nbsp; For both algorithm, we get
```
Total number of word: 200,
Number of correct: 153,
Number of incorrect: 47,
Accuracy: 0.765,
False Positive: 58,
False Negative: 21,
True Positive: 169,
True Negative: 478,
```

# Discussion
&nbsp;&nbsp;&nbsp;&nbsp; From where we have for CSC494, this project incorporated the considering of word pronunciation in matching two words. We have used a combination of string matching techniques like string different (fuzz method) and string pronunciation (Phonetic method). For the two phonetic methods, we chose to use Metaphone instead of Soundex since Soudex has too many duplicate codes for our medication bank. Also, we added another consideration of general words, in which to decrease the false positive rate, we will ignore to match any word that is in the general everyday English word bank. With the general word bank, our accuracy for using a combination of methods increased from 64% to 76.5%, which is a one-third decrease in error. With the two updates, our algorithm's accuracy had increased from 72.5% (accuracy for CSC494, where we use fuzz only) to 67% (use Metaphone only), 76% (use fuzz only), and 76.5% (use a combination of both methods). From our result, the best accuracy we got, 76.5%, had a decrease of 14.5% in error rate. 

&nbsp;&nbsp;&nbsp;&nbsp; Although we do have a large decrease in error rate, we still could consider how to improve our accuracy further. Another possible solution to improve accuracy is to find a better general word bank since our current one still missing some common English words like daily, been, taken, times, past, etc. With a larger general word bank, we expect an increase in overall accuracy and a decrease in the false positive rate.

&nbsp;&nbsp;&nbsp;&nbsp; In conclusion, the auto-correction algorithm represents a significant improvement towards ensuring the integrity and accuracy of medication data within the IMPACT pharmacogenetics study database. The algorithm also provides the foundation for any future related data analysis and computation.

# Reference
Bird, S., Klein, E., & Loper, E. (2009). Natural language processing with Python: analyzing text with the natural language toolkit. " O&#x27;Reilly Media, Inc."

Levenshtein, V. (1965). Binary codes capable of correcting spurious insertions and deletions of ones. Probl. Inf. Transmission, 1, 8–17. 

Russell, R. C., & Odell, M. K. (1918). Indexing-System (U.S. Patent No. 1261167). United States Patent and Trademark Office.

Mouselimis, L. (2021). fuzzywuzzyR: Fuzzy String Matching. R package version 1.0.5. 

Hanging on the Metaphone, Lawrence Philips. Computer Language, Vol. 7, No. 12 (December), 1990.

Nation, I.S.P. (2017). The BNC/COCA Level 6 word family lists (Version 1.0.0) 