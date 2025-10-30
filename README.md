# ceng442-assignment1-utkuakkusoglu
# Utku Akkuşoğlu - 22050111030

Google Drive Link for Trained Models: https://drive.google.com/drive/folders/15C0I0azeNzKWD24KM0H7kyxUuaTpI4V5?usp=sharing


1) The goal of this project was to learn how to clean and process five different Azerbaijani text datasets for sentiment analysis by examining the provided code, train using Word2Vec and FastText embedding models and compare Syn/Ant similarities, and learn how to build a lightweight domain-aware data pipeline. 

Setting the neutral value to 0.5 is a standard practice for mapping emotion onto a continuous scale. This approach preserves the full spectrum of emotion.


2) The preprocessing pipeline was created by applying the normalize_text_az() method to all texts. Rules:
- Emojis have been replaced with emotion labels EMO_POS and EMO_NEG (e.g. 👍 --> EMO_POS, 👎 --> EMO_NEG).
- Fixed character encoding errors with ftfy library.
- Stripped all HTML tags and unescaped HTML entities (e.g., &amp --> &).
- Replaced URLs, emails, phone numbers, and user mentions with special tokens as URL, EMAIL, PHONE, USER respectively.
- The # sign was removed from hashtags, their text was preserved but split into camelCase (e.g. #QarabagIsBack --> qarabag is back).
- Applied Azerbaijani-specific lowercase rules (e.g., İ→i, I→ı).
- Replaced all digits with a <NUM> token. 
- Removed standalone punctuation and extra spaces.
- Marked the three tokens following a negator (e.g., yox, deyil) with a _NEG suffix
- For Negation Handling purposes, 3 tokens following the words "yox","deyil","heç","qətiyyən","yoxdur" were marked using the _NEG suffix.
- Single letter tokens have been removed except "o" and "e".
- Dropped any rows with empty text or exact duplicate text entries.


3) Most of the mini-challenges listed in the assignment PDF were implemented directly within the main normalize_text_az() method:
- Hashtag split is implemented by using regex to find hashtags and then split camelCase
- Emoji mapping is implemented by using the EMO_MAP dictionary
- Negation scope is implemented using a mark_neg counter variable to append _NEG to the 3 subsequent tokens 
- Simple Deasciify was implemented using SLANG_MAP. I also extended it with other words to get a more accurate result by applying lemmatization, which is an optional part of the assignment.


4) Using the detect_domain function, each text in the original Excel files was divided into four different categories: news, reviews, social, and general. Detection was done using key regex expressions in NEW_HINTS, SOCIAL_HINTS, and REV_HINTS (e.g., "reuters" for news; "@" or some emojies for social; "azn", "ulduz" for reviews).

A domain_specific_normalize function was applied after the base cleaning. For the reviews domain, this function replaced price mentions (e.g., 100 azn) with <PRICE> and star ratings (e.g., 5 ulduz) with <STARS_5>. Also replaced "çox yaxşı" --> RATING_POS and "çox pis" --> RATING_NEG 

Added a domain tag (e.g., domnews, domgeneral) per line to the corpus_all.txt file with the build_corpus_txt() method.


5) | Parameter     | Word2Vec    |  FastText   |
   |---------------|----------   |-------------|
   | vector_size   | 300         | 300         |
   | window        | 5           | 5           |
   | min_count     | 3           | 3           | 
   | sg            | 1(Skip-gram)| 1(Skip-gram)|
   | negative      | 10          | X           |
   | epochs        | 10          | 10          |
   | min_n         | X           | 3           |
   | max_n         | X           | 6           |

   Lexical Coverage (per dataset):
   == Lexical coverage (per dataset) ==
   labeled-sentiment_2col.xlsx: W2V=0.932, FT(vocab)=0.932
   test__1__2col.xlsx: W2V=0.987, FT(vocab)=0.987
   train__3__2col.xlsx: W2V=0.990, FT(vocab)=0.990
   train-00000-of-00001_2col.xlsx: W2V=0.943, FT(vocab)=0.943
   merged_dataset_CSV__1__2col.xlsx: W2V=0.949, FT(vocab)=0.949

   Synonym/Antonym Similarity (My results before applying lemmatization):
   == Similarity (higher better for synonyms; lower better for antonyms) ==
   Synonyms: W2V=0.358, FT=0.435
   Antonyms: W2V=0.351, FT=0.432
   Separation (Syn - Ant): W2V=0.008, FT=0.003

   Synonym/Antonym Similarity (My results I got after applying a simple lemmatization):
   == Similarity (higher better for synonyms; lower better for antonyms) ==
   Synonyms: W2V=0.368, FT=0.426
   Antonyms: W2V=0.317, FT=0.396
   Separation (Syn - Ant): W2V=0.052, FT=0.029

   Nearest Neighbors (For lemmatized models):
   == Nearest neighbors (qualitative) ==
   W2V NN for 'yaxşı': ['<RATING_POS>', 'yaxshi', 'bist', 'yaxsidi', 'yaxsdi']
   FT  NN for 'yaxşı': ['yaxşıı', 'yaxşıkı', 'yaxşıca', 'yaxş', 'yaxşıki']
   W2V NN for 'pis': ['<RATING_NEG>', 'lire', 'vərdişlərə', 'qiyməyinin', 'həcmlərini']
   FT  NN for 'pis': ['piis', 'pisdii', 'pi', 'pisi', 'pixlr']
   W2V NN for 'çox': ['çoox', 'bəyən', 'çöx', 'çoxx', 'əladir']
   FT  NN for 'çox': ['çoxçox', 'çoxx', 'çoxh', 'ço', 'çoh']
   W2V NN for 'bahalı': ['metallarla', 'yaxtaları', 'portretlerinə', 'villaları', 'radiusda']
   FT  NN for 'bahalı': ['bahalıı', 'bahalısı', 'bahalıq', 'baharlı', 'pahalı']
   W2V NN for 'ucuz': ['ucuzdu', 'düzəltdirilib', 'şeytanbazardan', 'qiymətlər','keyfiyetli']
   FT  NN for 'ucuz': ['ucuzu', 'ucuza', 'ucuzdu', 'ucuzluğa', 'ucuzlawib']
   W2V NN for 'mükəmməl': ['möhtəşəmm', 'kəliməylə', 'mukəmməl', 'tamamlayır', 'öyrədici']
   FT  NN for 'mükəmməl': ['mükəmməll', 'mükəmməldi', 'mükəməl', 'mukəmməl', 'mükəmməlsiz']
   W2V NN for 'dəhşət': ['xalçalardan', 'ayranları', 'soundtreki', 'təsirlidi', 'birda']
   FT  NN for 'dəhşət': ['dəhşətdü', 'dəhşətə', 'dəhşətizm', 'dəhşəti', 'dəhşəttli']
   W2V NN for '<PRICE>': []
   FT  NN for '<PRICE>': ['reeceep', 'engiltdere', 'lifcikle', 'recebzade', 'cruise']
   W2V NN for '<RATING_POS>': ['deneyin', 'internetli', 'öyrədici', 'əladı', 'oynuyorum']
   FT  NN for '<RATING_POS>': ['<RATING_NEG>', 'süperr', 'süper', 'çookk', 'cöx']

   Domain Drift Analysis: As a optional analysis, two temporary Word2Vec models were trained (one on domnews corpus, one on domreviews) to measure domain drift. 
   == Domain Drift Scores (News vs Reviews) ==
   Drift for 'yaxşı': 0.6084
   Drift for 'pis': 0.6691
   Drift for 'çox': 0.5658


6) I implemented a simple, dictionary-based lemmatization to improve model quality.

Approach: Instead of a complex morphological analyzer, I expanded the existing SLANG_MAP to include common inflected forms of sentiment-related words (e.g., yaxşıdır, pisdi, bəyəndim, gəldi) and map them to their root forms (e.g., yaxşı, pis, bəyən, gəl). This approach is simple, fast, and effectively groups sparse tokens.

Effect: Even this simple addition I made had a significant, measurable impact, as I showed in question 5.


7) 
Versions: All required Python libraries and their exact versions are specified in requirements.txt

Seeds: The Gensim library results showed slightly different results across different runs, even though I set a seed value of 42. The results I present in this report (Separation = 0.052) are the exact output of the models I provided in the Google Drive link.

Machine: Training and testing were performed on a Windows 11 machine with an Intel i7 12700H CPU and 16GB RAM. The full pipeline runs in approximately 4-5 minutes.

How to Run: 
Option A: Verify Report Results
- Activate virtual environment
- Install dependencies: pip install -r requirements.txt
- Download the embeddings folder from the Google Drive link at the top of this README file
- Place the "embeddings" folder in the root of the project directory
- (Troubleshooting: If you get a FileNotFoundError for ...vectors_ngrams.npy, your browser may have renamed it to ...vectors_ngrams-001.npy. Please remove the -001.) This is what happened when I tested it on my virtual machine.
- Run the evaluate.py script (included in the repo) to load the models and print the exact evaluation metrics reported above: python evaluate.py

Option B: Retrain Models from Scratch
- Activate virtual environment
- Install dependencies: pip install -r requirements.txt
- Place the 5 original Excel datasets in the root of the project directory
- Run the main script: python main.py


8) For this task, FastText appears to be the more robust model. While Word2Vec achieved a better Syn-Ant separation score (0.052 vs 0.029), FastText's strength lies in its subword-based approach, and since Azerbaijani is an agglutinative language, this provides an additional advantage. The "Nearest Neighbors" results clearly show FastText handling morphological variations and typos, while Word2Vec tends to find more semantically related (but different) words. Given that sentiment data from social media and reviews is full of such typos, FastText would likely generalize better.
