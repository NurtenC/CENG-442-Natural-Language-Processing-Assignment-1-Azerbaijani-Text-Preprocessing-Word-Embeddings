# CENG-442-Natural-Language-Processing-Assignment-1-Azerbaijani-Text-Preprocessing-Word-Embeddings

21050111027 Nurten Çiftçioğlu

23050151030 Muhammed Enes Uğraş

---

Embeddings: [v200w2min1neg5epoc10fastt3_9](https://drive.google.com/drive/folders/1StuVwMDDu43o1ni5r-VAKz2e4SjgUWFZ?usp=sharing)

---

### 1) Data & Goal

The purpose of this assignment is to preprocess the train and test data for use in sentiment analysis and to obtain the best Word2Vec and FastText implementations for the Azerbaijani language by varying the hyperparameters such as vector size, window size, epoch count, and training methods (CBOW, Skip-gram). The dataset used in the assignment consists of five Azerbaijani text files that need to be processed. The lines in these texts are labeled as negative (0) and positive (1). There are also lines labeled as neutral, which must be assigned a numerical value. The chosen value for the neutral label is 0.5, because neutrality represents being neither positive nor negative. The value 0.5 is exactly between 0 and 1. For example, if the sentiment score is 0.6, it is only slightly positive, not strongly positive. As the score gets closer to 0, it becomes more negative. Therefore, 0.5 is a good choice because it stands in the middle and clearly represents a neutral feeling.

---

### 2) Preprocessing

For preprocessing, all words were converted to lowercase according to Azerbaijani language rules. URLs were replaced with the tag URL, emails with EMAIL, phone numbers with PHONE, and mentions with USER. HTML residues were cleaned, and # symbols were removed. Letters repeated three or more times were reduced to two repetitions. Digits were replaced with the <NUM> tag. Standalone punctuation marks and extra spaces were removed, while Azerbaijani-specific characters (such as ç, ö, ü, etc.) that do not exist in English were preserved. Single-character tokens other than *o* and *e* were removed. Completely duplicated or empty rows were deleted.

**Before → After** **Examples:** 



- Mən yeganə yerdir ki, buranın pizzalarını çox bəyənirəm, qiymətləri digər pizza restoranları ilə müqayisədə sərfəlidir. Bəzən elədikləri kompaniyalar çox yaxşı olur və mən də dəyərləndirməyə çalışıram. Pizzaların dadı möhtəşəmdi mənə görə. Telegram adım @muradaevv

   - ***yeganə yerdir buranın pizzalarını bəyənirəm qiymətləri digər pizza restoranları müqayisədə sərfəlidir bəzən elədikləri kompaniyalar yaxşı olur dəyərləndirməyə çalışıram pizzaların dadı möhtəşəmdi mənə görə telegram adım USER***

- Kiçikdən böyüyə hamı öz bacardığı qədər çapmağa niyyətlənib. Belin qırılsın #Azercell

    - ***kiçikdən böyüyə hamı öz bacardığı qədər çapmağa niyyətlənib belin qırılsın azercell***

- Coooooox pis şirkətdir. Bununla ikinci dəfədir kargoda problem yaşayıram. Türkiyeden birbaşa əməkdaşlıqla məşğuldurlar guya bağlama gedib çatmır gomruye. Tel işləmir Trendyola da 40 dəfə yazıram ne xeyri min zülmlə pulu geri göndərdilər o da günün mezennesibe göre mene sərf etmir axı

    - ***coox pis şirkətdir bununla ikinci dəfədir kargoda problem yaşayıram türkiyeden birbaşa əməkdaşlıqla məşğuldurlar guya bağlama gedib çatmır gomruye tel işləmir trendyola <NUM> dəfə yazıram ne xeyri min zülmlə pulu geri göndərdilər günün mezennesibe göre mene sərf etmir axı***




---

### 3) Mini Challenges
- Hashtag texts were split according to camel case. 
- Before tokenization, a small dictionary was built to map emojis to EMO_POS or EMO_NEG.  
- In addition to the existing stopwords (və, ilə, amma, ancaq, lakin, ya, həm, ki, bu, bir, o, biz, siz, mən, sən, orada, burada, bütün, hər, artıq, çox, az, ən, də, da, üçün), ten more stopwords were added: necə, şey, isə, hələ, nə, niyə, kimi, belə, indi, qədər.  All of these words also have equivalents in Turkish and English:  
(Azerbaijani→ Turkish → English) → (necə → nasıl → how), (şey → şey → thing), (isə → ise → whereas), (hələ → hâlâ → still/yet), (nə → ne → what), (niyə → niye → why), (kimi → gibi → like/as), (belə → böyle → like this/so), (indi → şimdi → now), (qədər → kadar → as much as/until).
    - After several training experiments we concluded that removing stopwords reduces synonym and antonym scores. Therefore, we decided not to use stopwords
- Based on the negator, the one preceding or the three following words were marked with the _NEG suffix.  
- A small ASCII mapping was also applied to convert words such as cox → çox and  yaxsi → yaxşı.

---

### 4) Domain-Aware

A four-class domain scheme was applied consisting of news, social, reviews, and general. Domain detection was performed using a rule-based approach relying on keyword and pattern matching. News texts were detected through keywords such as *apa, trend, azertac, reuters, bloomberg, dha,* and *aa*. Social texts were identified by the presence of *rt*, *@*, *#*, *USER*, and also by the use of emojis. Reviews were detected based on keywords such as *azn, manat, qiymət, aldım, ulduz, çox yaxşı,* and *çox pis*, as well as the domain-specific tags \<STARS\>, \<RATING_POS>, and \<RATING_NEG>. For this domain, additional normalization rules were applied: price expressions such as “20 manat” were replaced with the tag \<PRICE>, positive and negative rating phrases were substituted by \<RATING_POS> and \<RATING_NEG>, and star expressions (e.g., *5 ulduz*) were normalized to \<STARS_5>. General texts received only the basic Azerbaijani-aware cleaning without any further substitutions.  

Finally, after normalization, each sentence in the combined corpus was prepended with a domain tag token (e.g., domnews, domsocial, domreviews, domgeneral). These tags were automatically added using the add_domain_tag() function before writing the final corpus_all.txt ensuring that every line in the training corpus explicitly carries its detected domain context.

---

### 5) Embeddings

#### Training Settings and Results

| Feature | Word2Vec | FastText |
|----------|-----------|-----------|
| Vector Size | 200 | 200 |
| Window | 2 | 2 |
| Min Count | 1 | 1 |
| SG (0=CBOW, 1=Skip-gram) | 1 | 1 |
| Epochs | 10 | 10 |
| Coverage (%) | 100 | 100 |
| Seed | 42 | 42 |
| Negative Sampling | 5 | - |
| Min_n| - | 3 |
| Max_n| - | 9 |
| Avg Synonym Similarity |0.592  | 0.608 |
| Avg Antonym Similarity | 0.490 | 0.503 |
| Separation(Syn - Ant)  | 0.101 | 0.106 |


#### Nearest neighbors (qualitative)
- W2V NN for ***yaxşı***: `['<RATING_POS>', 'iyi', 'yaxşi', 'yaxshi', 'zor']`
- FT  NN for ***yaxşı***: `['yaxşıı', 'yaxşıfı', 'yaxşıkı', 'yaxşııdıı', 'yaxşıca']`
- W2V NN for ***pis***: `['<RATING_NEG>', 'bərbad', 'iyi', 'zay', 'berbat']`
- FT  NN for ***pis***: `['pispispis', 'pisdr', 'piss', 'piis', 'pissdii']`
- W2V NN for ***çox***: `['çoox', 'coox', 'temu', 'chox', 'bəyənirəm']`
- FT  NN for ***çox***: `['ççox', 'çoxçox', 'çoxx', 'çoh', 'ço']`
- W2V NN for ***bahalı***: `['acılı', 'nəhəng', 'kefiyyətsiz', 'növbələriniz', 'detallara']`
- FT  NN for ***bahalı***: `['bahalıı', 'bahalısı', 'baharlı', 'bahalıdı', 'bahaymış']`
- W2V NN for ***ucuz***: `['münasib', 'baha', 'qiymətə', 'qiymete', 'toyuğu']`
- FT  NN for ***ucuz***: `['ucuzlu', 'ucuzluk', 'ucuza', 'ucuzu', 'ucuzdu']`
- W2V NN for ***mükəmməl***: `['möhtəşəm', 'yararlı', 'süper', 'möhtəşəmm', 'süjetli']`
- FT  NN for ***mükəmməl***: `['mükəmməll', 'mükəmməldii', 'mükəmməldiçox', 'mükemməl', 'qeyrimükəmməl']`
- W2V NN for ***dəhşət***: `['hədsiz', 'çoox', 'gülməlidir', 'çoxx', 'kanaldır']`
- FT  NN for ***dəhşət***: `['dəhşətt', 'dəhşətdü', 'dəhşətdie', 'dəhşətizm', 'dəhşətiymiş']`
- W2V NN for ***<PRICE\>***: `['qəpik', 'kartdan', 'dollar', 'aylıq', 'azn']`
- FT  NN for ***<PRICE\>***: `['manatmıpul', 'manatdısa', 'manatdığ', 'manatlığ', 'manats']`
- W2V NN for ***<RATING_POS\>***: `['süper', 'yaxşi', 'superdir', 'əlverişli', 'əla']`
- FT  NN for ***<RATING_POS\>***: `['süperr', 'süper', 'zuperr', 'çooxxzorr', 'yaxşiliğa']`

---

### 6) Lemmatization

We found two sources for Azerbaijani lemmatization Apertium and MorAz. However, we were unable to integrate them into the model.

---

### 7) Reproducibility

- Versions:
    - Python: 3.12.12
    - pandas: 2.2.2
    - ftfy: 6.3.1
    - gensim: 4.4.0
    - openpyxl: 3.1.5

- Seed: 42

- Machine: Google Colab CPU


- How to run: 
    - Run 1stpart_nlp_assignment.ipynb in Colab or your computer
        - Assign your paths to drive_path_raw and drive_path_cleaned
    - Run 2ndpart_nlp_assignment.ipynb in Colab or your computer
        - Assign your paths to drive_path_cleaned and drive_path_embeddings
    - Run 3rdpart_nlp_assignment.ipynb in Colab or your computer
        - Assign your paths to drive_path_cleaned and drive_path_embeddings
     
---

### 8) Conclusion

Between the two models FastText performed slightly better than Word2Vec. Both models achieved full vocabulary coverage but FastText produced higher synonym and antonym separation. Before training we already expected better performance from FastText because it uses subword n-grams which help it capture affixes and morphological variations of the same word more effectively. Since Azerbaijani is a morphologically rich language this capability is particularly important. As seen in the synonym lists for the seed words FastText was able to identify words that were both semantically and syntactically similar, while Word2Vec mostly found only semantically related ones. This shows that FastText can generalize across different word forms making it the more suitable model for Azerbaijani text representation.
