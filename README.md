# CENG-442-Natural-Language-Processing-Assignment-1-Azerbaijani-Text-Preprocessing-Word-Embeddings

### 1) Data & Goal

The purpose of this assignment is to preprocess the train and test data for use in sentiment analysis and to obtain the best Word2Vec and FastText implementations for the Azerbaijani language by varying the hyperparameters such as vector size, window size, epoch count, and training methods (CBOW, Skip-gram). The dataset used in the assignment consists of five Azerbaijani text files that need to be processed. The lines in these texts are labeled as negative (0) and positive (1). There are also lines labeled as neutral, which must be assigned a numerical value. The chosen value for the neutral label is 0.5, because neutrality represents being neither positive nor negative. The value 0.5 is exactly between 0 and 1. For example, if the sentiment score is 0.6, it is only slightly positive, not strongly positive. As the score gets closer to 0, it becomes more negative. Therefore, 0.5 is a good choice because it stands in the middle and clearly represents a neutral feeling.

---

### 2) Preprocessing

For preprocessing, all words were converted to lowercase according to Azerbaijani language rules. URLs were replaced with the tag URL, emails with EMAIL, phone numbers with PHONE, and mentions with USER. HTML residues were cleaned, and # symbols were removed. Letters repeated three or more times were reduced to two repetitions. Digits were replaced with the <NUM> tag. Standalone punctuation marks and extra spaces were removed, while Azerbaijani-specific characters (such as ç, ö, ü, etc.) that do not exist in English were preserved. Single-character tokens other than *o* and *e* were removed. Completely duplicated or empty rows were deleted.

**Before → After** **Examples:** 



Mən yeganə yerdir ki, buranın pizzalarını çox bəyənirəm, qiymətləri digər pizza restoranları ilə müqayisədə sərfəlidir. Bəzən elədikləri kompaniyalar çox yaxşı olur və mən də dəyərləndirməyə çalışıram. Pizzaların dadı möhtəşəmdi mənə görə. Telegram adım @muradaevv

***yeganə yerdir buranın pizzalarını bəyənirəm qiymətləri digər pizza restoranları müqayisədə sərfəlidir bəzən elədikləri kompaniyalar yaxşı olur dəyərləndirməyə çalışıram pizzaların dadı möhtəşəmdi mənə görə telegram adım USER***

Kiçikdən böyüyə hamı öz bacardığı qədər çapmağa niyyətlənib. Belin qırılsın #Azercell
***kiçikdən böyüyə hamı öz bacardığı qədər çapmağa niyyətlənib belin qırılsın azercell***

Coooooox pis şirkətdir. Bununla ikinci dəfədir kargoda problem yaşayıram. Türkiyeden birbaşa əməkdaşlıqla məşğuldurlar guya bağlama gedib çatmır gomruye. Tel işləmir Trendyola da 40 dəfə yazıram ne xeyri min zülmlə pulu geri göndərdilər o da günün mezennesibe göre mene sərf etmir axı
.
***coox pis şirkətdir bununla ikinci dəfədir kargoda problem yaşayıram türkiyeden birbaşa əməkdaşlıqla məşğuldurlar guya bağlama gedib çatmır gomruye tel işləmir trendyola <NUM> dəfə yazıram ne xeyri min zülmlə pulu geri göndərdilər günün mezennesibe göre mene sərf etmir axı***




---

### 3) Mini Challenges
- Hashtag texts were split according to camel case. 
- Before tokenization, a small dictionary was built to map emojis to EMO_POS or EMO_NEG.  
- In addition to the existing stopwords (və, ilə, amma, ancaq, lakin, ya, həm, ki, bu, bir, o, biz, siz, mən, sən, orada, burada, bütün, hər, artıq, çox, az, ən, də, da, üçün), ten more stopwords were added: necə, şey, isə, hələ, nə, niyə, kimi, belə, indi, qədər.  All of these words also have equivalents in Turkish and English:  
(Azerbaijani→ Turkish → English) → (necə → nasıl → how), (şey → şey → thing), (isə → ise → whereas), (hələ → hâlâ → still/yet), (nə → ne → what), (niyə → niye → why), (kimi → gibi → like/as), (belə → böyle → like this/so), (indi → şimdi → now), (qədər → kadar → as much as/until).  
- Based on the negator, the one preceding or the three following words were marked with the _NEG suffix.  
- A small ASCII mapping was also applied to convert words such as cox → çox and  yaxsi → yaxşı.

---

### 4) Domain-Aware

A four-class domain scheme was applied consisting of news, social, reviews, and general. Domain detection was performed using a rule-based approach relying on keyword and pattern matching. News texts were detected through keywords such as *apa, trend, azertac, reuters, bloomberg, dha,* and *aa*. Social texts were identified by the presence of *rt*, *@*, *#*, *USER*, and also by the use of emojis. Reviews were detected based on keywords such as *azn, manat, qiymət, aldım, ulduz, çox yaxşı,* and *çox pis*, as well as the domain-specific tags \<STARS\>, \<RATING_POS>, and \<RATING_NEG>. For this domain, additional normalization rules were applied: price expressions such as “20 manat” were replaced with the tag \<PRICE>, positive and negative rating phrases were substituted by \<RATING_POS> and \<RATING_NEG>, and star expressions (e.g., *5 ulduz*) were normalized to \<STARS_5>. General texts received only the basic Azerbaijani-aware cleaning without any further substitutions.  

Finally, after normalization, each sentence in the combined corpus was prepended with a domain tag token (e.g., domnews, domsocial, domreviews, domgeneral). These tags were automatically added using the add_domain_tag() function before writing the final corpus_all.txt ensuring that every line in the training corpus explicitly carries its detected domain context.
