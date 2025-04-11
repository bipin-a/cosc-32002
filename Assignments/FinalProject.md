1. N-grams

   - BLEU score (used actively)
   - Predict next word (but sucks)

3. Baysian Models (HMM)

   - Part of Speech Tagging
   - Still used in other industries
   - 

4. Embedding Models

   - Word2Vec (similarity defined not by meaning but by proximity in corpus)
      -  Static (no good) 1 word has 1 vector regardless of meaning/semantics
   - The output of a Dot Product is a raw similarity.
   - The transformation of a dot product can be characterized by (eigin value and eigin vector)

Neural Network:
    - Prone to vanish gradient if we use a sigmoid activation function. (because math) 
        - Relu (change our activation function)
        - Residual Networks (ResNets)
        - Use less layers

6. RNNs ()

   - Neural Network
   - Weight can compound and explode or vanish. (eigen value)

8. LSTM ()

- Bi-Directional LSTm
- Stacked LSTMs
- etc.

5. Self-attention Mechanisms

   - Update the embedding based on the context! (this means one word can have many vectors representing it) wowza
   - Positional Encoding
   - ??

7. LLMs(BERT, GPT)

   - Pre-Trained Models (Understanding vocabulary)
   - Fine Tune (understanding the domain)
   - RAG
   - Prompt: Context Windows in Prompts



9. Retrieval Augmented Generator (RAG)

Use Case: Shifting context. 

a. Looking up information from N pages or data sources (PDFs, url etc.)
b. Specialize based off a corpus.
c. Fine tuning but less resources?

Cons:
a. Hallucinations. 
b. 


