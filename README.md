# Spooky Author Identification using Neural Networks


Competition goal is to predict the author of excerpts from horror stories by Edgar Allan Poe, Mary Shelley, and HP Lovecraft

However, the goal of my experiments was not to get the best score possible, but to experience myself how several neural networks variants react to the sentence representation options. 


Competition link: [link](https://www.kaggle.com/c/spooky-author-identification) <br>
All the models are implemented using [allennlp](https://github.com/allenai/allennlp) lib

To reproduce experiments, fetch allennlp's commit specified at requirements.txt.   

[Competiton rules](https://www.kaggle.com/c/spooky-author-identification/rules) allow for use of the data for academic goals, 
so I attached prepared data to this repo (/data folder): `preprocessed-train.txt`, `preprocessed-dev.txt`, `tc-tok-test_public_X.txt`
## Steps to reproduce
1) Download glove embeddings and put to to the data/glove folder: <br>
`https://s3-us-west-2.amazonaws.com/allennlp/datasets/glove/glove.6B.100d.txt.gz`
2) Train a model: <br>
`python -m run train experiments_configs/authors_classifier.json -s experiments_models/initial_run`<br>
(you can choose another .json config to train another model, or choose another save folder)
3) [Optional] Compute metrics values for your model on dev set: <br>
`python -m run evaluate --archive_file experiments_models/initial_run/model.tar.gz --evaluation_data_file data/preprocessed-dev.txt --cuda_device -1`
4) Convert test set to the json lines format: <br>
`python3 data_utils/predict_utils.py data/tc-tok-test_public_X.txt data/tc-tok-test_public_X.jsonl`
5) Translate the test set: <br>
`python -m run predict experiments_models/initial_run/model.tar.gz data/tc-tok-test_public_X.jsonl --output-file data/submission-test-public_Y.jsonl`
6) Convert test set to the Kaggle submision format: <br>
`python3 data_utils/predict_utils.py data/submission-test-public_Y.jsonl data/submission-test-public_Y.csv`

## Data preprocessing
The data is first split into dev (3000 points) and train sets (code is in the /data_utils folder), and then truecased and tokenized using [moses scripts](https://github.com/marian-nmt/moses-scripts)    

## Models (WIP)
General idea is the same:
1) Encode source sentence to get single vector sentence representation
2) Project this representation onto desired classes

Different approaches differ in a way the encode the sentence <br>
Sentence encoding variations I experimented with: <br> 
- RNN over word embeddings -> take last encoder output 
- RNN over word embeddings -> concatenate all encoder outputs
- CNN over word embeddings 

- char-RNN with explicit word segmentation
- char-CNN with explicit word segmentation

- char-RNN without explicit word segmentation
- char-CNN without explicit word segmentation

- RNN over word embeddings + CNN based word characters embeddings
- RNN over word embeddings + linguistic tegs

- RNN over word embeddings + linguistic tegs + CNN based word characters embeddings

## Future work
It is interesting also to see how rubust are results across different datasets. 