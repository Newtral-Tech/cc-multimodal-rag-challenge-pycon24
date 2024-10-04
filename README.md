# MultiModal RAG

## Context
A ClaimReview is a metadata standard to capture relevant information about a fact-check
(e.g. title, image, rating, claim…). Because the claim review document combines text and
multimedia resources, a search engine or assistance chatbot should take into account not
only the textual information, but also all the information that can be extracted from
multimedia resources (in our case, restricted to image resources).

In this context, the objective of the challenge is to build a MultiModal RAG
(Retrieval-Augmented Generation) system that, when faced with a question asked in text or
image: (1) retrieves the most relevant claim review candidates and (2) generates a response,
considering, in both phases, that images attached to the claim review document can also
provide relevant information.

NOTE: If you need more information about ClaimReview schema, please access [Fact Check (ClaimReview) Markup for Search](https://developers.google.com/search/docs/appearance/structured-data/factcheck?hl=es).

## Task definition
The objective is to develop a RAG (Retrieval-Augmented Generation) able to generate a
response enhanced using information extracted from the images. The search space will be
an extract of our claim review database with records including claim review, derived
textual data as summary or keywords, and image that contextualize or complement the
article (see below the dataset description).

There is a dual objective:
- Better document selection in the retrieval phase, thanks to the information derived from
images
- Better responses generated based on the use of images, by enhanced the text response
and/or including images 

In line with these general objectives, one or more of the following techniques (not mutually
exclusive) could be further explored:
- Using base multimodal embeddings
- Encoding and retrieving each modality separately with a re-ranker that considers both
- Considering only text in the retrieval, including both the selected text field(s) for
this purpose and the textual description of the image generated with some multimodal model… and subsequently using a re-ranker that can consider both modalities.


![RAG pre processing workflow](https://developer-blogs.nvidia.com/wp-content/uploads/2024/03/rag-preprocessing-for-images.png)


### Other considerations

- Part of the task includes retrieving the images using the URLs associated with each record.
- The retrieval phase could be evaluated on the basis of the match between the search terms
(unverified claim column) and the claim (similarity = 1, match; similarity = 0, no match).
Nevertheless, a part of the task involves proposing an alternative evaluation method for the
system, either end-to-end and/or for the different parts of the process.
- The system should always respond that it does not have enough information to respond to the
user if it does not find relevant documents in the search space (i.e. the dataset provided)
- While not the main objective of the task, special consideration will be given to the correct
interpretation of graphs or diagrams due to the potential impact their understanding can have
on improving retrieval and the final response. For instance, when presenting a diagram, a
“not ideal” textual output would be: a bar chart with information about <topic>; whereas an
ideal textual output would be: a chart showing how the level of <data> was rising over the last
<period_of_time>. The latter would help the system to do a more efficient retrieval 
- Initially, the solution is considered to be developed with the LlamaIndex framework.

## The Data
The attached dataset contains verified/reviewed claims available in our claim review database.
It includes contextual information about these verifications/reviews, such as full text, summary,
url, description, keywords, dates or attached multimedia resources (image, video). Also, each
record includes a similarity relation (established by humans) indicating the match
(“similarity” = 1) of not match (“similarity” = 0) between the claim review and a search term
(or unverified claim) provided in the “unverified claim” field.

### Field description

#### Basic info
* **reviewed claim** *(str, multilingual)*: reviewed claim
* **title** *(str, multilingual)*: article title
* **text** *(str, multilingual)*: original text. It can contains "noise" in the sense of including 
some incorrect paragraphs (texts of menus, whitespaces, symbols, etc.)
* **summary** *(str, multilingual)*: summary generated from original text (using an external service)

#### Multimedia resources
* **cr_image** *(str, url)*: (if any) image attached into the claim review
* **meta_image** *(list[str], url)*: (if any) linked images extracted from the meta and/or json+ld object
* **movies** *(list[str], url)*: (if any) videos linked on the meta and/or json+ld object

#### Other metadata
* **meta_description** *(str, multilingual)*: description extracted from meta tags and/or json+ld representation
* **kb_keywords** *(list[str], multilingual)*: keywords generated from original text (ngrams range 1-3) using KeyBert
* **meta_keywords** *(list[str], multilingual)*: keywords extracted from meta tags and/or json+ld representation
* **url** *(string, url)*: url of the claim review
* **domain** *(string, url)*: domain (of the publisher)
* **published** *(datetime)*: publication date
* **cm_authors** *(list[str], multilingual)*: a list of authors (extracted from meta tags and/or json+ld representation)
* **cr_author_name** *(str): name of the author of the claim review (the fact-checker)
* **cr_country** *(string)*: country of publication
* **meta_lang** *(string, ISO Code)* native language of the claim review extracted from meta tags

#### Similarity relation 
* **unverified claim** *(str, multilingual (en, es))*: unverified claim / search term
* **similarity** *(int)*: label indicating a positive (1) or negative (0) match between a search term
(or unverified claim) and the verified claim


## Useful links
[An Easy Introduction to Multimodal Retrieval-Augmented Generation](https://developer.nvidia.com/blog/an-easy-introduction-to-multimodal-retrieval-augmented-generation/)

[Multi-modal applications - LlamaIndex](https://docs.llamaindex.ai/en/stable/use_cases/multimodal/)
