# Hybrid-Text-Data-Summarization
A Hybrid Text Summarization using KL-Divergence, Greedy Sentence Selection and Fine-tuned using MLE &amp; PPO

Text summarization is the automated process of shortening long pieces of text into summary,understandable formats that retain the most important information from their initial source. As wenavigate this new information era, characterized by an exponential increase in digital text from news sources, academic research, reports, and the web, the need to swiftly glean pertinent insights is essentialfor effective learning and informed decision-making. Summarization meets this fundamental need and delivers easy-to-digest summaries that save precious seconds while allowing users to quickly grasp a document’s main point without the need to dive into the full text. The methodologies developed to do so largely fit neatly into one of two buckets: extractive summarization, which pulls important sentences or phrases directly from the source text and compiles them, and abstractive summarization, which produces entirely new sentences to express the source’s key messages, frequently with a different lexicon and syntax.

This system features a novel hybrid text summarizer that carefully balances extractive and abstractive approaches to create high-quality, coherent, and factually consistent summaries. This multistage pipeline is designed to first extract the most salient information from the source text and then paraphrase it fluently, taking advantage of the best of both statistical methods for grounding and neural models for generation, further tuned with reinforcement learning to match human evaluation metrics. Before any of the core summarization processes can begin, input documents go through a far-too-often overlooked but severely important preprocessing stage. This first step, called sentence splitting, is where the document is first divided into the individual sentences of the document, then each sentence is tokenized, or broken down into their corresponding words or sub-word units that are used in the downstream models.

The text is also changed to the lowercase for normalization purposes, and non-alphanumeric characters and common stop-words, which do not convey much semantic value for the KL-divergence computation is typically removed or filtered, although stop-words may be included for the T5 model based on its pretraining needs.
The first big stage in the summarization pipeline is the extractive phase where you try to identify the most salient subset of sentences that accurately represent the full content the best way possible. This stage uses a KL-Divergence minimization approach. To start, the system predicts a probability distribution for every word (P) over the whole preprocessed source document, creating a first-pass representation of the document’s central thematic substance. After determining the document’s initial word count balance, the extractive phase iteratively constructs a potential summary. At every step, it looks at appending each remaining sentence from the source document to the current best candidate summary. In order to evaluate each possible addition, a new word probability distribution (Q) is generated for the candidate summary. Next, the KL-divergence, KL(P||Q), between the original full document distribution P and the candidate summary distribution Q is calculated, informing the selection of the sentence showing the least divergence, so that the summary accurately reflects the content in the document. This greedy sentence extraction method continues until a specified number of sentences, usually set as a ratio of the original sentence count, are chosen which together
create the extractive summary.

This noisy summary, here turned into a clean and informative version of the source article, is fed into the next abstractive stage. For this particular task, the system uses a pre-trained T5 model, the ‘t5-large’ variant, which is well known for its performance on summarization tasks. The T5 model was selected based on its powerful text-to-text framework, allowing it to generate novel sentences that paraphrase and synthesize the information in the extractive input. To improve the faithfulness and informativeness of the resulting summaries more towards specific evaluation metrics (especially ROUGE), the model is further fine-tuned with Reinforcement Learning (RL). This final stage fine-tunes the T5 model specifically to maximize the ROUGE score, a common collaboration SLA metric used in the summarization quality space. The REINFORCE algorithm is employed, along with a Self-Critical Sequence Training (SCST) baseline, a method that has been shown to work well for a variety of sequence generation tasks.

System Architecture:
              ![image](https://github.com/user-attachments/assets/edf0b7f6-5ad8-408f-9256-f9dc03d6fc53)

Within this RL framework, our fine-tuned T5 model serves as the policy, whose job is to generate summaries. The reward function for a generated summary (y) given an input extractive summary (x) is simply its ROUGE-L F1 score against the human reference summary. To lessen the high variance that is often associated with policy gradient methods, an SCST baseline is used. For each input, two summaries are produced from the current policy: a sampled summary (s) created by sampling from the model’s output distribution, and a baseline summary (g) produced by greedy decoding. The advantage is computed as the difference in expected rewards between the sampled (non-greedy) summary and the greedy summary, Advantage = R(s)−R(g). This advantage signal lets the model know if the output that was sampled was an improvement over its naive greedy best. The model’s parameters are then iteratively refined using a policy gradient estimate that includes this advantage. The update rule is designed to maximize the expected reward, changing the policy to prefer actions (token generations) that yield higher ROUGE scores, i.e. J(θ) (R(s) – R(g)) ∑s log π(s|x). The RL training loop traverses the dataset with a specialized data loader. For each batch, sampled (s) and greedy (g) summaries are produced with a reference model (a snapshot of the policy model) to stabilize training.

The overall flow chart of this proposed system is below,
                ![image](https://github.com/user-attachments/assets/fb5b388d-ebe7-4802-952c-46cda722d9aa)


Training Results & Output:
![image](https://github.com/user-attachments/assets/d23b2ccd-795e-4d08-b828-8f7ee26493e5)

![image](https://github.com/user-attachments/assets/ba163a10-33ff-483f-aca3-2b7ffedf26e3)


Generated Summary:
![image](https://github.com/user-attachments/assets/fcea4c0c-81f9-494a-b21a-2bcd0c109404)

Evaluation Metrics & Accuracy Scores:
![image](https://github.com/user-attachments/assets/4fbaaf38-2a56-4d0d-8255-efbedd74a6ca)



