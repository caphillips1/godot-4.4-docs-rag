# Godot 4.4 Documentation RAG

This project builds a local vector database from the official Godot Engine 4.4 documentation. It’s designed for developers who want to query the docs semantically instead of digging through pages manually.

The script (`godot_4_4_documentation_rag.py`) and notebook (`Godot_4_4_Documentation_RAG.ipynb`) do the same thing — one is runnable as a plain Python file, the other is structured for use in Google Colab.

---

## What It Does

- Clones the [Godot 4.4 docs repo](https://github.com/godotengine/godot-docs)
- Finds and parses all `.rst` files into plain text
- Splits the text into overlapping chunks for better context retention
- Embeds each chunk using the `all-MiniLM-L6-v2` transformer model
- Stores everything in a local [Chroma](https://www.trychroma.com/) vector database
- Optionally saves the vector DB to Google Drive (if run in Colab)

---

## Dependencies

Install these first:

```
pip install -U langchain langchain-community langchain-huggingface sentence-transformers chromadb docutils
```

If you're using the notebook, it'll handle installs for you. Otherwise, run the above in your terminal.

---

## Usage

Once the vector database is created and saved, you can load it and run semantic queries. Here's a minimal example:

```python
from langchain_community.vectorstores import Chroma
from langchain_huggingface import HuggingFaceEmbeddings

db_path = "/path/to/chroma_db"
embeddings = HuggingFaceEmbeddings(model_name="sentence-transformers/all-MiniLM-L6-v2")
vectordb = Chroma(persist_directory=db_path, embedding_function=embeddings)

results = vectordb.similarity_search("how to create a 2d character animation")

for r in results:
    print(r.page_content)
```

You can ask questions in plain English. The database will return relevant sections of the docs, even if your phrasing doesn’t match the original wording.

---

## Output

The key output is a folder called `chroma_db` that contains the vectorized documentation. This folder is what you load when querying the database. You can copy it to your Google Drive or any local directory.

---

## Notes

- The script wipes any existing `chroma_db` directory before creating a new one.
- You’ll need to re-run the whole script if you want to update the docs or change the embedding model.
- Expect a few minutes of processing time, especially during the embedding step.

---

## When to Use This

Use this if:

- You want to build a chatbot or assistant for Godot 4.4 docs
- You’re tired of keyword-based search
- You want to integrate Godot help directly into your own tools or pipelines
