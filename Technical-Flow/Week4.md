15 Technical Questions — Week 4

RAG Concepts

Q1. What does RAG stand for? Explain what Retrieval and Generation mean separately and then how they work together.

Ans: RAG stands for Retrieval-Augmented Generation. Retrieval means searching our saved files to find relevant info, and Generation means the AI writes a clean answer using that info. Together, they make sure the AI answers using real facts instead of guessing.

Q2. What is chunking? You used 500-word chunks with 50-word overlap. What does the overlap do and why is it important?

Ans: Chunking means splitting long PDFs into small paragraphs. The 50-word overlap repeats a few words between chunks so that important sentences or context don't get accidentally cut in half.

Q3. What is an embedding? Why does converting text to numbers allow you to search for similar meaning instead of just matching exact words?

Ans: An embedding converts words into a list of numbers based on their meaning. Since similar ideas get similar number codes, the AI can match sentences that mean the same thing even if the actual words are different.

Q4. What is a vector database? How is ChromaDB different from a regular database like MySQL or Excel?

Ans: A vector database stores text as meaning-based numbers. MySQL or Excel can only search for exact word matches, but ChromaDB searches by understanding the actual meaning of the query.

Q5. What is cosine similarity? In simple terms explain how ChromaDB uses it to rank which chunks are most relevant to a question.

Ans: Cosine similarity is a math formula that checks how close two meanings are. ChromaDB calculates this score between the question and all chunks, bringing the highest-matching chunks to the top.

Code & Implementation

Q6. You used the model all-MiniLM-L6-v2 for embeddings. What does this model do? Why did you not use the LLM (LLaMA 3) for embeddings as well?

Ans: This model turns text into meaning vectors very quickly. We don't use heavy LLMs like LLaMA 3 for embeddings because all-MiniLM-L6-v2 is lightweight, fast, and specifically built for similarity search.

Q7. In the ChromaDB collection you set "hnsw:space": "cosine". What does HNSW mean and why did you specify cosine as the distance metric?

Ans: HNSW is a fast search method used to find matching vectors quickly in big databases. We set it to cosine because cosine distance works best for measuring similarity between text concepts.

Q8. In the RAG function you set temperature=0.2. In Week 3 you used temperature=0.7. Why is a lower temperature better for a RAG system?

Ans: Low temperature (0.2) keeps the AI strictly factual and bound to the document text. A high temperature makes the AI creative, which can cause it to make up false facts.

Q9. In Step 3 you used chunk_size=500 and overlap=50. What happens to retrieval quality if you set chunk_size to 50? What if you set it to 5000?

Ans: A chunk size of 50 is too tiny and cuts off sentences in the middle. A size of 5000 is too huge, mixing multiple topics together and making search inaccurate.

Q10. You stored 3 things in ChromaDB for each chunk — documents, embeddings, and metadatas. What is metadata and why did you store the source document name with each chunk?

Ans: Metadata is extra info attached to a chunk. Storing the file name as metadata lets the AI cite the exact document source (like policy_2.txt) in its final answer.

Evaluation & Understanding

Q11. What is the difference between fit_transform() in Week 2 (TF-IDF) and encode() in Week 4 (sentence-transformers)? What does each one do to text?

Ans: fit_transform() only counts exact word frequencies using basic math, while encode() uses a neural network to capture the actual context and meaning of whole sentences.

Q12. In Step 8 you asked about salary policy — which is NOT in any document. What did your RAG system return? Why is this the correct and safe behaviour for an enterprise system?

Ans: It replied that the information was not in the knowledge base. This is correct because in a company, saying "I don't know" is much safer than giving a false or guessed answer.

Q13. What is the n_results parameter in the ChromaDB query? You used 3. What are the trade-offs of using 1 vs 10 chunks as context?

Ans: n_results sets how many context chunks to retrieve. Pulling 1 chunk might miss details, while pulling 10 chunks brings too much extra noise and can confuse the AI.

Q14. You ran the RAG pipeline with a system prompt saying "If the answer is not in the context say..." Why is this instruction in the system prompt and not in the user message?

Ans: The system prompt sets the master behavioral rules for the AI model that stay active across all user questions, ensuring safety rules are never bypassed.

Q15. What is the difference between semantic search and keyword search? Give an example where keyword search would fail but semantic search would succeed using your IT policy documents.

Ans: Keyword search looks for exact character matches, while semantic search looks for meaning. Searching "fix portal lock" fails on keyword search if the doc says "account login block", but semantic search finds it easily.

15 Scenario Based Questions — Week 4

Q1. An employee asks about VPN, but gets password chunks. What went wrong and how to fix it?

Ans: The vector search matched the wrong chunk because the embedding scores were too close. I can fix this by adjusting chunk size or adding clearer metadata tags to the documents.

Q2. Your RAG system gives a correct answer but cites the wrong document name. What could cause this and how to fix it?

Ans: The source filename metadata was mapped incorrectly when loading chunks into ChromaDB. I can fix it by re-verifying the file metadata assignment during data ingestion.

Q3. Employees complain answers are too long. What changes would you make without changing the RAG pipeline?

Ans: I would change the system prompt to ask for "short, point-wise answers" and lower the max_tokens generator parameter.

Q4. You add a new PDF policy, but the chatbot can't answer questions about it yet. What 3 steps do you need to do?

Ans: 1. Load and chunk the new PDF 2. generate embeddings for those chunks, and 3. insert the new embeddings and metadata into ChromaDB.

Q5. Teammate used chunk size 50 (precise retriever, poor answers) vs your size 500 (good answers). How to explain this trade-off?

Ans: Tiny chunks locate exact words easily, but they lack the surrounding context sentences needed for the LLM to generate a complete, meaningful answer.

Q6. ChromaDB in Colab won't work for 500 simultaneous users. What would you replace it with?

Ans: I would upgrade from local ChromaDB to a cloud vector database like Pinecone, Milvus, or hosted Qdrant.

Q7. A user asks for the CEO name, and the system invents a confident fake name. Is this a RAG or prompt failure? How to fix?

Ans: It's a prompt failure because the AI fell back on its general memory. I can fix it by strengthening the system prompt to strictly forbid answering outside the given context chunks.

Q8. Retriever returns 3 chunks from the exact same paragraph, missing the real answer elsewhere. What happened and how to fix?

Ans: The search pulled duplicate paragraphs due to high overlap. I would enable Maximal Marginal Relevance (MMR) search to force diverse chunk results.

Q9. Why download NIST government documents instead of uploading custom company documents?

Ans: NIST files provide standardized, comprehensive enterprise IT policy content, making them an ideal, safe benchmark for testing RAG pipelines.

Q10. A user asks in Hindi, but all documents are in English. What happens and how to handle it?

Ans: Standard search fails because English and Hindi vectors won't align. I can fix this by adding a translation layer or using a multilingual embedding model like multilingual-e5.

Q11. For a simple question like "What is a password?", no-RAG is better than RAG. Why and is it a problem?

Ans: No-RAG uses general pre-training knowledge, which is fine for dictionary terms. It's not a problem, because RAG is specifically designed for private company files, not general definitions.

Q12. How would you use metadata in ChromaDB to restrict HR policies to HR employees and IT policies to IT?

Ans: I would add a department tag in the chunk metadata (e.g., department: "HR"), and then filter ChromaDB queries based on the logged-in user's department role.

Q13. Colab restarted and ChromaDB was deleted, but PDFs remain. What to re-run, what to skip, and how to fix permanently?

Ans: I only need to re-run the chunking, embedding, and ChromaDB loading script (skipping PDF download). To fix permanently, I would persist the vector database to cloud storage like AWS S3.

Q14. VPN answers are great, but incident response answers are vague. Why and how to fix?

Ans: The incident response PDF might have poor text layout or missing sections during extraction. I would check those raw chunks, clean the text, and re-chunk the document.

Q15. A non-technical manager says: "This is just a search engine, we already have Google." How to explain RAG in simple language?

Ans: A search engine just gives you a list of links to open and read yourself. RAG actually reads through all those internal files for you instantly and gives you a single, accurate answer to your exact question. It's like having a personal assistant who knows all company documents by heart.