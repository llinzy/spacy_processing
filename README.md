# spacy_processing

success_on_all_56000rows_but_slow.ipynb:
This Spacy run is successful, but slow.  It takes 28 minutes to process all 56,000 rows.


To speed up process, I leveraged Spacy's pipeline processing feature.

fail_on_spacy_speedup_greater1000rows.ipynb:
This run is faster, but successful only up to 1000 rows at a time.
I reduced the default batch from 128 to 80 to obtain the success for
1000 rows.  I reduced the batch size to 20 to attempt success with 5000 rows, 
but not successful.
