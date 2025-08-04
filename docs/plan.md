# **Developing Stand-Alone Python Programs for Ontology Development and Information Extraction**

## **1\. Executive Summary**

This plan outlines a focused strategy for developing pure Python stand-alone programs dedicated to ontology development and information extraction. These programs will be designed for command-line execution and seamless integration as modules within other Python applications. The core objective is to leverage advanced natural language processing (NLP) techniques, particularly Large Language Models (LLMs), to extract, structure, and enrich biological knowledge from scientific literature, and to manage this knowledge within a robust, plant-specific ontology.

The architecture will prioritize modularity, ensuring each component can function independently while being easily importable. Key Python libraries such as Owlready2 will be central for comprehensive ontology management, including programmatic trimming, filtering, and defining complex hierarchical relationships. For information extraction, tools like paperscraper, PyMuPDF, pdfplumber, LLM-IE, and OntoGPT will be employed for corpus building, text preprocessing, Named Entity Recognition (NER), and sophisticated relationship extraction using LLMs. Post-extraction, text2term, FuzzyWuzzy, dedupe, multitax, and ncbi-taxonomist will be crucial for mapping extracted entities to the ontology, normalizing data, and integrating taxonomic information. Rigorous testing and the creation of gold standard datasets will ensure the accuracy and reliability of the extracted knowledge. This approach will yield highly reproducible, efficient, and valuable scientific software for advancing plant metabolomics research.

## **2\. Introduction to the Project**

### **Project Vision and Goals**

The overarching vision is to develop a comprehensive backbone annotated metabolites network. This network aims to significantly enrich existing metabolomics data by providing deeper biological insights into the functional roles of plant metabolites and their relevance to plant resilience. The project will establish a sophisticated system capable of automatically extracting, annotating, and contextualizing plant metabolomics data from scientific literature. This automation is crucial for accelerating discoveries in plant science and enhancing our ability to engineer more resilient plant systems.

### **Importance of LLMs in Information Extraction and Ontology Enrichment**

Large Language Models (LLMs) are transformative for processing and understanding unstructured text, offering unparalleled capabilities for extracting complex, contextual information from scientific literature. This is particularly vital for enriching metabolomics datasets, which often lack detailed functional annotations. Traditional methods struggle to capture the nuanced biological relationships and functional contexts embedded within free-text scientific articles. LLMs bridge this gap by enabling the automated identification of entities (e.g., metabolites, genes, traits) and the extraction of complex relationships between them (e.g., "Compound A affects Trait B," "Gene C is involved in Pathway D"). This capacity to derive rich, contextual annotations from literature is critical for transforming raw metabolomics data into actionable biological understanding, facilitating the generation of high-quality hypotheses, and accelerating research into plant resilience.

### **Overview of Functional Requirements**

To achieve its ambitious deliverables, the project necessitates the development of a diverse suite of interconnected functionalities, specifically focusing on ontology development and information extraction. These include:

* Systematic development and management of a refined plant-specific ontology.  
* Sophisticated application of LLMs for extracting information from scientific literature.  
* Precise mapping of extracted entities and relationships to the defined ontology.  
* Rigorous evaluation and benchmarking of the LLM-driven extraction processes.

Each of these components is crucial for establishing a comprehensive system that can automatically extract, annotate, and contextualize plant metabolomics data, thereby enhancing the understanding of metabolite functions and their roles in plant resilience.

## **3\. Core Python Project Architecture and Best Practices**

The successful execution of this project hinges on a meticulously planned and robust software architecture. Adopting industry best practices for Python development will ensure the system is not only functional but also scalable, maintainable, and conducive to collaborative development, all while adhering to the "pure Python stand-alone" and "command-line testable" requirements.

### **Modular Design for Stand-alone and Importable Programs**

The project will adopt a highly modular programming paradigm, systematically breaking down large, intricate programming tasks into smaller, more manageable subtasks or modules.1 This approach is fundamental for simplifying the development process, significantly enhancing maintainability, and promoting extensive code reusability across different components of the system.1

Each major functionality, such as Ontology Management or LLM Extraction, will be encapsulated within its own distinct Python package or a top-level module within the overarching project structure. This clear separation of concerns ensures that each component can be developed, tested, and maintained independently. Internal dependencies between these modules will be explicitly declared using import module.submodule statements, a practice that is critical for maintaining code readability and ensuring clear compartmentalization of functionalities.2 The use of wildcard imports, such as

from module import \*, will be strictly avoided, as this practice can lead to namespace pollution and obscure the explicit dependencies within the codebase, making it harder to track where functions or classes originate.2

Adherence to PEP 8 guidelines will be enforced for naming conventions, ensuring module names are short, lowercase, and utilize underscores for word separation when necessary.1 For hierarchical organization, submodules will be employed for namespacing rather than relying on underscores within module names (e.g.,

library.plugin.foo is preferred over library.foo\_plugin).2 This structured naming convention ensures that Python's import mechanism functions correctly and significantly enhances the clarity and navigability of the codebase. Furthermore, the architecture will incorporate abstraction layers to effectively separate different concerns, such as isolating data acquisition logic from information extraction processes . This design choice promotes clean interfaces, reduces coupling between components, and allows for independent evolution of different layers of the system.

For a project with the explicit requirement for "stand-alone but also importable programs," modularity is not merely a stylistic choice but a strategic imperative. The project's scope suggests involvement from a diverse team of experts, and a modular design directly facilitates parallel development efforts by different team members or sub-teams on distinct components. This approach minimizes integration challenges and reduces the likelihood of version conflicts, thereby streamlining the overall development workflow. Moreover, the "importable" nature of the programs directly benefits from this well-defined modular structure, as individual components can be easily packaged and reused within other research initiatives or as integral parts of a larger, integrated system. This forward-thinking design ensures the long-term viability, extensibility, and collaborative efficiency of the codebase.

### **Dependency Management Strategy**

Effective management of Python dependencies is crucial for maintaining project stability, ensuring reproducibility, and simplifying deployment. For this project, Poetry will be adopted as the primary dependency management tool.3

Poetry provides a modern and organized solution that encompasses not only dependency management but also project packaging and build processes.3 It leverages a

pyproject.toml file to specify project configuration and dependencies, coupled with an automatically generated poetry.lock file. This lockfile precisely records the exact versions of all direct and transitive dependencies, ensuring that the project can be consistently reproduced across different development and deployment environments.3 This approach addresses common limitations found in traditional

requirements.txt and environment.yaml files, such as often producing lengthy dependency lists without associated metadata and lacking a default mechanism for locking specific package versions.3 Poetry also allows for the intelligent separation of dependencies into development-specific and production-specific categories within the

pyproject.toml file, further streamlining deployment environments and mitigating the risk of conflicts.3

Given the explicit exclusion of Docker, any non-Python dependencies must be meticulously handled. Libraries like Owlready2 (which can use Java-based reasoners like HermiT 4) or

text2term (which might have underlying non-Python components for certain mappers 5) require specific underlying binaries. These non-Python dependencies must be explicitly documented for manual installation on the target system. Alternatively,

Conda can be considered, as it functions as both a package and environment manager capable of handling both Python and non-Python dependencies within its environments, offering a more integrated solution for complex scientific stacks.3 This combination of Poetry (or Pipenv 6) and careful documentation of system-level prerequisites is particularly beneficial for scientific research, where the reproducibility of computational environments is a core principle. By ensuring deterministic builds and providing isolated environments, this strategy significantly reduces the overhead and potential risks associated with setting up the system on new machines, thereby minimizing "dependency hell" and ensuring a smooth transition from development to operational use.

### **Internal API Design Principles and Code Organization**

The design of internal Application Programming Interfaces (APIs) within the project will adhere to principles that prioritize usability, maintainability, and performance.

A guiding principle will be **Miller's Law**, which suggests that the human brain has a limited capacity, typically able to hold no more than 7±2 chunks of information simultaneously . This principle will inform the design of APIs to minimize cognitive overhead for developers interacting with different modules. Interfaces will be simplified, aiming to present a concise set of functions, classes, and parameters at any given time .

**Discoverability** will be enhanced through clear, consistent naming conventions, logical organization of modules, and leveraging auto-completion features in integrated development environments (IDEs) . This allows developers to easily explore and understand the available functionalities. **Explicitness** will be maintained by clearly differentiating between public (user-facing) and internal API features. This distinction helps guide developers on which components are stable for external use and which are subject to internal refactoring, thereby aiding long-term maintenance . The design will also prioritize **maintenance**, ensuring that internal refactoring of components can occur without introducing breaking changes to external users of the API . An explicit list of public symbols will be maintained to help identify potential issues or unintended exposures during development .

**Performance** considerations are integral to API design. For interactions with external services (e.g., PubMed API), optimizations will include requesting only the necessary data fields and utilizing the most specific filters available (e.g., employing "is" for exact matches rather than "contains" for partial matches) . Furthermore, for frequently accessed read-only data, the implementation of caching layers will be considered to alleviate unnecessarily repetitive calls to external services, thereby improving overall system responsiveness .

A crucial architectural decision involves implementing a dedicated **API isolation layer or wrapper** for interactions with external services such as PubMed. This layer will insulate the project's internal tools from potential changes or instabilities in external APIs, centralize error handling, and facilitate debugging and auditing of external calls .

In terms of **code organization**, the project will maintain a well-structured directory layout, with separate top-level folders for the core codebase, data assets, testing suites, and comprehensive documentation.7 Consistent naming conventions, such as using underscores for variables and functions and capitalized camel case for classes, will be strictly enforced across the entire project.7 Finally, a comprehensive

README.md file will be included at the project root, providing a concise introduction, outlining the project's purpose, detailing installation instructions, and guiding users on how to utilize the various components.7

### **Testing Frameworks and Continuous Integration**

To ensure the robustness, reliability, and scientific integrity of the project's outputs, a rigorous testing strategy and continuous integration practices will be implemented. Pytest will serve as the primary testing framework, chosen for its extensive feature set and flexibility .

While Pytest offers powerful features, its more "magical" aspects, such as implicit fixtures and non-sandboxed execution, will be managed through strict internal guidelines. Direct imports of pytest and unittest will be restricted to a dedicated internal testing framework module. This approach prevents the misuse of advanced features and ensures that only necessary, wrapped utilities are exposed to feature developers . Common Pytest functionalities, including pytest.raises for exception handling, pytest.mark.parametrize for managing multiple test cases, and freezegun.freeze\_time for time mocking, will be encapsulated within helper functions in this central testing module. This encapsulation simplifies test writing, promotes consistency, and provides clear documentation for their proper use .

For testing interactions with external dependencies, particularly external APIs, moto can be employed for in-memory simulation of services (though less relevant for direct API calls like PubMed, it's a general principle for external dependencies). This ensures that tests are fast, reliable, and isolated from actual service calls. Boilerplate code for setting up and tearing down these mocked services will be abstracted into decorators, guaranteeing test isolation and simplifying the developer experience . To streamline the creation of diverse test data, a collection of "Fakers" will be implemented. These functions will be designed to return specific fake objects based on defined data types, significantly reducing the boilerplate code typically required for manually defining well-structured test data .

Test coverage will be systematically monitored using coverage-py, which generates detailed reports on test coverage per module . This critical resource will identify gaps in the test suite, allowing development efforts to be focused on areas requiring more robust testing. All code will be managed under Git for version control.7 It is paramount that the dependency lock file (e.g.,

pyproject.toml and poetry.lock) are committed to version control. This practice ensures consistent and reproducible environments across all development machines and continuous integration/continuous deployment (CI/CD) pipelines.6 Furthermore,

Ruff, Flake8, and Black will be integrated into the development workflow to enforce code consistency with PEP 8 guidelines and to automatically identify common programming mistakes early in the development cycle.7

The disciplined testing strategy, encompassing wrapped utilities, mocked services, comprehensive coverage, and consistent code style, directly underpins the reliability of the information extraction pipelines and LLM outputs. This rigorous approach ensures that the data processing and information extraction are accurate and consistent, thereby building confidence in the scientific credibility of the project's findings and facilitating their acceptance in the broader research community. This commitment to quality also makes the project more resilient to future changes and contributions, as new features or modifications can be integrated with confidence, knowing that existing functionalities are thoroughly validated.

## **4\. Ontology Development and Management**

A foundational component of the project is the development and robust management of a refined ontology. This ontology will serve as the structured knowledge base for plant metabolites, their sources, and their functions, enabling deeper biological insights.

### **Strategy for Trimming and Filtering Existing Ontologies**

The project necessitates trimming and filtering existing, often expansive, ontology categories to create a more manageable and useful subset specifically tailored for metabolite resolution in the context of plant resilience. For instance, the goal is to reduce a large set of anatomical terms (e.g., 2008\) to a more focused and relevant subset (e.g., 293).

The primary Python package for this programmatic manipulation of OWL 2.0 ontologies will be Owlready2.8 The methodology will involve several key steps:

1. **Loading Ontologies:** Relevant existing ontologies will be loaded into Owlready2 using its get\_ontology().load() function.9 This allows the ontology to be represented as Python objects, enabling programmatic access and manipulation.10  
2. **Identification of Relevant Terms:** Criteria will be developed to precisely identify terms to be retained. This may involve keyword matching, analysis of hierarchical relationships, filtering based on specific properties, or intersection with a pre-defined core set of plant-specific terms. Owlready2's search() method can be used to query for specific classes, individuals, or properties based on various criteria such as IRI, type, subclass relationships, direct relationships (is\_a), property values, or annotations.11  
3. **Filtering and Selection:** Owlready2 provides methods for accessing and manipulating ontology content, which can be used to implement filtering logic.12 For example, iterating through instances of a class using the  
   .instances() method and applying custom criteria.14  
4. **Deletion of Irrelevant Entities:** Programmatic use of the destroy\_entity() global function in Owlready2 will be employed to remove irrelevant classes, individuals, or properties, along with all their associated relations, from the ontology.15  
5. **Export:** The refined and trimmed ontology will then be exported to standard formats such as OWL/RDF/XML.8 These formats are widely accepted for semantic web data, ensuring interoperability and persistent storage.

The explicit need for "trimming and filtering existing ontology categories to a more manageable and useful subset" underscores a critical aspect of knowledge representation. An overly broad or noisy ontology would lead to increased computational overhead for downstream LLM tasks, reduce the relevance and precision of extracted biological insights, and make the knowledge base significantly more complex to manage and query. By meticulously filtering the ontology, the project ensures that the LLMs are "grounded" in a highly relevant and concise domain. This direct approach improves the accuracy and efficiency of information extraction and subsequent analyses, serving as a foundational enabler for achieving the project's goal of "deeper biological insights."

### **Designing the Refined Ontology Scheme (Structural, Source, Function)**

The refined ontology scheme will be explicitly structured into three primary, interconnected categories as specified in the project requirements: Structural Annotation, Source Annotation, and Functional Annotation. This categorization provides a clear, logical framework for organizing diverse biological knowledge.

* **Structural Annotation:** This category will integrate terms from established chemical and natural product classification systems. Specifically, it will draw upon Chemont classification, NP Classifier, and the Plant Metabolic Network (PMN) to represent the chemical structures and classifications of metabolites.  
* **Source Annotation:** This section will focus on the biological origin and experimental context of metabolites. Terms will be integrated from Plant Ontology for anatomical structures, NCBI Taxonomy for species identification, and the Plant Experimental Condition Ontology (PECO) for experimental context.  
* **Functional Annotation:** This category will capture the biological roles and effects of metabolites. Terms will be combined from Gene Ontology (GO) for molecular traits, Trait Ontology for broader plant traits, and ChemFont for human traits, where relevant for comparative or translational insights.

Owlready2 will be the primary tool used to define and manage these integrated terms, their properties, and their categorization within the overarching ontology.8

### **Defining and Managing Hierarchical Relationships**

The ontology will meticulously define and manage various hierarchical and semantic relationships between terms. These relationships are crucial for establishing a rich, interconnected knowledge graph that enables complex querying and inference. Specific relationship types to be captured include "is\_a," "made\_via," "accumulates\_in," and "affects."

Owlready2 natively supports the definition of complex relationships through its ObjectProperty and DataProperty classes.18 Furthermore, it allows for the specification of logical constructs such as

domain and range for properties, defining the types of entities that can participate in a given relationship.18 The

inverse\_property attribute can be used to define reciprocal relationships (e.g., if "has\_ingredient" is defined, "is\_ingredient\_of" can be its inverse), which Owlready2 automatically handles.18 More advanced property types like

FunctionalProperty (ensuring a single value for a given instance), TransitiveProperty (allowing inference of indirect connections, e.g., A is related to B, B is related to C, therefore A is related to C), and SymmetricProperty can also be defined, enriching the semantic expressiveness of the ontology.18

A critical feature of Owlready2 is its built-in reasoning capabilities, powered by HermiT or Pellet reasoners.4 These reasoners can infer new facts and automatically reclassify instances and classes based on the defined relationships and logical axioms. For example, if a "Compound A accumulates\_in Plant Part B" and "Plant Part B is\_a Root," the reasoner can automatically infer that "Compound A accumulates\_in Root".4

The conceptual model of "SQL Ontologies," while distinct from OWL, provides valuable guiding principles for treating relationships as first-class entities with inherent semantic meaning.20 This approach emphasizes that relationships are not merely connections but carry rich contextual information, supporting inheritance (e.g., an "Artist" inherits properties from a "Person") and transitive reasoning (e.g., an employee indirectly reports to a director through a manager).20 This conceptual clarity will guide the

Owlready2 implementation, ensuring that the defined relationships are robust and enable deeper biological inference.

The explicit inclusion of various relationship types and the emphasis on their management is fundamental to achieving the project's goal of "deeper biological insights." By meticulously defining these hierarchical and functional relationships within the ontology and leveraging Owlready2's reasoning capabilities, the project can move beyond simple entity recognition to infer complex, implicit biological connections. This capability is paramount for understanding the "functional roles of plant metabolites" and their contribution to plant resilience, as it allows for the automatic expansion and contextualization of knowledge, thereby facilitating the discovery of novel biological associations.

### **Storage and Version Control of Ontology Terms**

For persistent storage and collaborative development, the ontology terms will be maintained in standard OWL or CSV files, as specified in the project requirements. OWL files are preferred for representing the complex semantic structures and relationships inherent in the ontology, while CSV files can be utilized for simpler, tabular lists of terms or annotations. Owlready2 provides robust support for importing and exporting OWL 2.0 ontologies in widely accepted formats such as RDF/XML or NTriples 8, ensuring broad compatibility and accessibility.

All ontology files, along with the entire project codebase, will be rigorously managed on GitHub. This ensures comprehensive version control, providing a complete history of all changes, facilitating collaborative development among team members, and serving as a robust backup mechanism. The use of version control is critical for maintaining the integrity and traceability of the evolving knowledge base.

### **Table 1: Proposed Ontology Categories and Integrated Sources**

The following table provides a structured overview of the project's foundational ontology, detailing the three primary categories and the external ontologies from which terms will be integrated. This structured representation offers a clear and concise reference for all stakeholders and new team members, enabling a quick understanding of the scope and structure of the project's knowledge base. It consolidates crucial information, highlighting the specific external ontologies that contribute to each internal category, thereby ensuring alignment on the foundational knowledge representation and aiding in overall project planning.

| AIM2 Ontology Category | Description | Integrated External Ontologies | Purpose/Role in AIM2 Project |
| :---- | :---- | :---- | :---- |
| **Structural Annotation** | Describes the chemical structures and classifications of metabolites. | Chemont classification, NP Classifier, Plant Metabolic Network (PMN) | Provides standardized chemical classifications and structural properties for plant metabolites. |
| **Source Annotation** | Details the biological origin and experimental context of metabolites. | Plant Ontology (anatomical structure), NCBI Taxonomy (species), Plant Experimental Condition Ontology (PECO) | Contextualizes metabolites by their location in plants, species of origin, and experimental conditions. |
| **Functional Annotation** | Captures the biological roles, activities, and effects of metabolites. | Gene Ontology (GO categories for molecular traits), Trait Ontology (plant traits), ChemFont (human traits) | Links metabolites to molecular functions, biological processes, cellular components, and relevant plant/human traits. |

## **5\. Literature Information Extraction using LLMs**

A cornerstone of the project is the automated extraction of structured information from scientific literature using Large Language Models (LLMs). This process involves building a comprehensive corpus, performing Named Entity Recognition (NER), and extracting complex relationships.

### **Building a Comprehensive Corpus of Plant Chemical Literature**

The foundation for LLM-driven information extraction is a comprehensive corpus of plant chemical literature. This corpus will be built from diverse sources, including PubMed, PubMed Central (leveraging its XML format), PDFs, and specific scientific journals.

For PubMed and PubMed Central, programmatic access will be achieved using Python libraries. Biopython's Bio.Entrez module provides a robust interface for querying NCBI's Entrez databases, including PubMed, allowing for keyword-based searches and retrieval of article IDs . Once IDs are obtained, Bio.Entrez.efetch can retrieve full records, including abstracts and XML content . Alternatively, the pubmed-xml package offers a dedicated parser for PubMed XML data, simplifying the extraction of relevant information from these structured files.22

For PDF documents, a combination of Python libraries will be employed to extract text, tables, and figures. PyMuPDF (also known as Fitz) is a powerful library for extracting text, images, and layout information from PDFs.23 For structured data within PDFs,

Camelot and Tabula-py are excellent choices for extracting tables into Pandas DataFrames, particularly from PDFs with clear line-defined tables.26

pdfplumber also offers precise control over PDF data extraction, including text with layout preservation, detailed table analysis, and image extraction.26

When accessing content from specific scientific journals through web scraping, careful consideration of bot protection mechanisms and ethical guidelines is essential. Strategies to address bot protection include rotating IP addresses using proxy services, employing custom User-Agent headers to mimic legitimate browser requests, and potentially utilizing specialized services like Bright Data's Web Unlocker, which automatically bypasses CAPTCHAs and JavaScript challenges . The paperscraper Python package can facilitate scraping publication metadata or full-text files (PDF or XML) from various sources, including those requiring API keys for major publishers like Wiley and Elsevier.27 It is imperative to always consult and comply with a website's

robots.txt file and adhere to legal and ethical considerations to avoid overloading servers, violating terms of service, or infringing on intellectual property rights .

### **Text Parsing, Cleaning, and Chunking for LLM Input**

Once the literature corpus is assembled, a critical preprocessing step involves parsing, cleaning, and chunking the text for optimal LLM input. This process is essential for optimizing the relevance of content and ensuring that embedding models can fit data within their context windows.28

**Text Cleaning and Preprocessing:** This involves standardizing formats and resolving inconsistencies.29 Key steps include:

* **Text Normalization:** Converting text to lowercase (with caution for proper nouns/acronyms), removing special characters (e.g., HTML tags, XML fragments, odd Unicode characters) using regular expressions.29  
* **Tokenization:** Breaking text into individual words or meaningful chunks.29  
* **Duplicate Removal:** Identifying and eliminating exact or near-duplicate entries using fuzzy matching algorithms.29  
* **Stopword Filtering:** Removing common words that may not carry significant meaning in specific contexts, while tailoring the stopword list to the biomedical domain.29  
* **Encoding Standardization:** Ensuring consistent text encoding, typically UTF-8, to prevent processing errors.29

  Libraries like spaCy and NLTK are valuable for these preprocessing tasks, offering sophisticated capabilities for tokenization and sentence segmentation.30

**Text Chunking:** This involves dividing larger documents into smaller, semantically coherent segments that fit within the LLM's context window and contain relevant information for downstream tasks.28 Strategies include:

* **Fixed-size chunking:** Dividing text into segments of a predetermined token count, often aligned with the embedding model's maximum context window size.28  
* **Sentence chunking:** Using libraries like NLTK or spaCy to split text into sentences, which helps preserve context within chunks.30  
* **Recursive Character Level Chunking:** LangChain's RecursiveCharacterTextSplitter can split text using a defined order of separators (e.g., \["\\n\\n", "\\n", " ", ""\]) to break paragraphs, sentences, and words based on a given chunk size.31  
* **Structure-aware chunking:** For documents like PDFs, HTML, or Markdown, utilities in LangChain or custom parsers can recognize structural elements (headers, paragraphs, lists) to create more semantically coherent chunks.31

The careful execution of text cleaning and chunking is paramount for optimizing LLM performance. If chunks are too small, they may lack sufficient context; if too large, they can introduce noise or dilute the significance of specific information, making precise retrieval difficult.28 The aim is to create chunks that are self-contained and meaningful to an LLM, much as they would be to a human reader, thereby directly impacting the quality and relevance of information extracted for the project.

### **Named Entity Recognition (NER)**

Named Entity Recognition (NER) is a crucial step for identifying and extracting specific entities from the processed text. For the project, this includes chemicals, metabolites, genes, proteins, species, plant anatomical structures, experimental conditions, molecular traits, plant traits, and human traits.

Various NER models and approaches will be considered:

* **Fine-tuned BERT/CyBERT Models:** These models, pre-trained on large text corpora and then fine-tuned on biomedical or domain-specific datasets, offer high accuracy for specific entity types. Examples include BioBERT which significantly outperforms previous state-of-the-art models on biomedical NER tasks.33  
  Spark NLP provides NerDLModel as an annotator that uses various embeddings for entity extraction.34  
* **LLM-based Approaches (Zero-shot/Few-shot NER):** Recent advancements in LLMs enable high-accuracy NER without extensive entity-specific fine-tuning, leveraging their broad linguistic understanding for "zero-shot" NER.35 Models like  
  Llama 70B, Gemma, and ChatGPT-4.1 can be employed. The Python package LLM-IE is specifically designed for biomedical generative information extraction using LLMs, supporting NER tasks with low annotation requirements through zero-shot and few-shot learning.37  
  OntoGPT is another Python package that extracts structured information from text using LLMs, instruction prompts, and ontology-based grounding.1  
* **Prompt Engineering:** For LLM-based NER, meticulously designed prompts are critical.33  
  LLM-IE includes an interactive LLM agent, the "Prompt Editor," which assists users in crafting effective prompts by providing access to pre-stored templates and guidelines.37 This iterative process of prompt design is crucial for optimizing extraction accuracy.

The selection of NER models will consider the trade-off between performance, computational resources, and the need for domain-specific fine-tuning. For biomedical entities, specialized models or carefully engineered prompts for general LLMs are often necessary due to specialized vocabularies and the complexity of biomedical terminology.33 The ability to accurately identify and classify these diverse entities is the first critical step in transforming unstructured text into structured knowledge for the project.

### **Relationship Extraction**

Beyond identifying individual entities, the project requires extracting complex relationships between them. This involves understanding how a compound "affects" a plant trait, or how a metabolite is "involved in" a biological process, often requiring context comprehension across large text regions.

LLMs, particularly with sophisticated prompt engineering, are well-suited for this task.37 The

LLM-IE Python package, for instance, supports relation extraction tasks and provides an interactive LLM agent to aid in schema definition and prompt design.37 Its "Sentence Frame Extractor" method has demonstrated superior accuracy in information extraction, including relationships, though it may require more processing time.38

Handling hierarchical relationships is also crucial to differentiate between broad and specific associations (e.g., distinguishing "involved in" from "upregulates" or "induces expression"). While LLM-IE focuses on general relation extraction, frameworks like RELRaE (Relationship Extraction, Labelling, Refinement, and Evaluation) demonstrate how LLMs can be integrated into multi-stage methods for generating accurate relationship labels, particularly from hierarchical structural information within XML schemas.41 Similarly,

HERCULES (Hierarchical Embedding-based Recursive Clustering Using LLMs for Efficient Summarization) uses LLMs to generate semantically rich titles and descriptions for clusters at different levels of a hierarchy, which could be adapted for understanding hierarchical relationships between extracted entities.42

The process will involve defining clear schemas for the target relationships and then crafting prompts that guide the LLMs to extract these specific connections. This may involve providing examples (few-shot learning) within the prompt to demonstrate the desired output format and relationship types. The ability to extract these complex, contextual relationships is fundamental for building the annotated metabolites network, as it directly contributes to understanding the functional roles of plant metabolites and their relevance to plant resilience. This moves beyond simple data points to a network of interconnected biological knowledge.

## **6\. Ontology Mapping and Post-processing**

Once entities and relationships are extracted from literature, they must be rigorously mapped to the defined ontology and undergo post-processing to ensure data quality and consistency.

### **Mapping Extracted Entities and Relationships to Ontology Terms**

The process of mapping extracted entities and relationships to the predefined ontology terms is critical for standardizing the unstructured information derived from literature. This mapping can occur either by providing LLMs with ontology terms prior to extraction to guide their output, or by mapping extracted entities to the ontology in a post-extraction step.

The text2term Python package is a powerful tool for programmatically mapping free-text descriptions of entities to controlled terms in ontologies.5 It supports various input formats (lists of strings, files, dictionaries with tags) and offers multiple mapping methods, including

TFIDF, Levenshtein, Jaccard, Jaro-Winkler, BioPortal, and Zooma.5

text2term allows for setting a minimum similarity score and can be configured to map to specific term types (classes, properties, or any) within the ontology.5 It also supports caching ontologies for repeated use, which significantly improves performance.5

Another relevant tool is OntoGPT, which focuses on extracting structured information from text with LLMs and performing "ontology-based grounding".1 This means the extracted information is directly linked to or validated against existing ontologies, ensuring semantic consistency and standardization. For biomedical entity linking, the

BioEL Python package provides a standardized framework for linking mentions of entities (e.g., genes, proteins, diseases) to their corresponding entries in a Knowledge Base, supporting an extensive collection of ontologies.44

This mapping process is vital because it transforms heterogeneous, free-text extractions into a semantically consistent and interoperable format. By grounding extracted data in the ontology, the project ensures that information from diverse sources can be integrated and analyzed cohesively, which is essential for building a unified knowledge network.

### **Normalizing Entity Names and Deduplicating Redundant Facts**

After initial mapping, a crucial post-processing step involves normalizing entity names and deduplicating redundant facts. This improves data quality, reduces storage requirements, and enhances the accuracy of downstream analyses.

**Normalization:** Entity names extracted from various literature sources may have inconsistencies in capitalization, spacing, or phrasing. Python's built-in string methods (e.g., str.lower(), str.title()) can be used for basic normalization . For more complex cases involving variations or misspellings, fuzzy string matching libraries like FuzzyWuzzy can be employed. FuzzyWuzzy provides functions such as fuzz.ratio, fuzz.partial\_ratio, fuzz.token\_sort\_ratio, and fuzz.token\_set\_ratio to compare strings and identify approximate matches, even when words are out of order . This helps in standardizing variations of the same entity name.

**Deduplication and Entity Resolution:** Redundant facts or duplicate entries referring to the same real-world entity are common in data compiled from multiple sources.45

dedupe is a Python library that uses machine learning to perform de-duplication and entity resolution on structured data.46 It learns optimal rules from human-labeled training data to identify similar records, even in large datasets.46 The

recordlinkage library is another Python package for record linkage and entity matching, offering classifiers (e.g., Logistic Regression, Naive Bayes) to categorize record pairs as matches or non-matches . These tools are essential for consolidating and cleaning the extracted knowledge base, ensuring a single, accurate representation for each unique entity.

Normalization and deduplication are fundamental for data integrity, particularly in a knowledge graph that integrates information from diverse and often noisy sources. Without these steps, the network would be plagued by inconsistencies and redundancies, leading to inaccurate analyses and misleading biological conclusions.47 By systematically cleaning and consolidating the extracted facts, the project ensures that the knowledge base is reliable, efficient to query, and provides a coherent view of plant metabolomics data. This meticulous data curation is a prerequisite for generating trustworthy scientific insights.

### **Incorporating NCBI Taxonomy for Robust Species Identification**

Robust species identification and filtering out irrelevant species are critical for contextualizing plant metabolomics data. The NCBI Taxonomy database is a primary resource for this.

Python libraries like multitax 5 and

ncbi-taxonomist 48 will be integrated to manage and utilize NCBI taxonomic information.

* multitax is a Python package that provides a common set of functions to download, parse, filter, explore, translate, and convert multiple biological taxonomies, including NCBI.5 It allows for filtering specific taxonomic branches or descendants, enabling the project to focus on relevant plant species and filter out others.5  
* ncbi-taxonomist is a command-line tool (with Python implementation) that collects and manages taxonomic data from NCBI's Entrez taxonomy database.48 It can fetch information on-demand, map between taxonomic IDs (taxids) and names, resolve lineages, and extract specific ranks or subtrees.48 It also allows for local storage of taxonomic information in an SQLite database, which can be useful for offline operations and creating experiment-specific taxonomy databases.48

The integration of NCBI Taxonomy is crucial for ensuring the biological accuracy and specificity of the project's knowledge base. By precisely identifying species and filtering out irrelevant ones, the project ensures that the extracted and annotated data is highly relevant to plant metabolomics and resilience. This level of taxonomic rigor prevents the inclusion of confounding data from non-plant organisms or incorrect species assignments, thereby enhancing the precision and utility of the generated biological insights.

## **7\. Evaluation and Benchmarking**

Rigorous evaluation and benchmarking are essential to validate the accuracy and performance of the LLM-driven information extraction pipelines, ensuring the reliability of the project's outputs.

### **Building Gold Standard Test Sets**

To objectively benchmark extraction accuracy, gold standard test sets of manually annotated papers will be constructed. A suggested initial target is approximately 25 papers. The process of creating these high-quality labeled datasets will adhere to established guidelines 51:

* **Detailed Annotation Guide:** A comprehensive, living document will be developed, providing clear rules, definitions for entities and relationships, and illustrative examples. This guide will be continuously updated as ambiguities arise and rules are refined.51  
* **Iterative Rule Refinement:** Early in the project, the focus will be on defining clear and non-overlapping annotation rules. Unclear examples will be discussed collectively, and rules will be refined iteratively. The cost of changing rules increases significantly as the project progresses, emphasizing the importance of early clarity.51  
* **Quality Control:** Double annotation, where multiple human annotators independently label the same data, will be implemented. Inter-annotator reliability analysis will be performed to measure consistency and identify areas of disagreement, which then inform rule refinement or annotator training.51  
* **Labeling Tools:** Investing in a good labeling user interface and workflow is crucial for annotator productivity and data quality.51 While pre-packaged tools like  
  Spacy Prodigy or Argilla exist, the project may consider building custom annotation/curation tools that can be quickly adapted to specific needs and integrated with the project's idiosyncratic tech stack.53 Custom tools can allow for quick sorting, filtering, and labeling, which is critical for iterative refinement of LLM-generated data.53  
* **Quality over Quantity:** A smaller, high-quality dataset is preferable to a large, low-quality one. Falsely labeled examples can mislead the model and skew evaluation metrics, increasing training time and cost.51 The focus will be on ensuring the accuracy and consistency of annotations.  
* **Data Versioning:** The labeled data will be versioned alongside the trained LLM models. This is critical for reproducibility, debugging, and tracking the evolution of the evaluation benchmarks.51

The creation of these gold standard datasets is not merely a technical step but a foundational element for building trust in the LLM-derived insights. Manually inspecting and curating data provides invaluable understanding of the problem space and the model's performance, directly contributing to both evaluation and fine-tuning efforts.53

### **Benchmarking LLM Performance**

With the gold standard test sets in place, rigorous benchmarking of different LLM models will be conducted for entity and relationship extraction tasks. This will involve evaluating models such as Llama, Gemma, and GPT-4o (or other suitable models) against the manually annotated data.36

Performance will be measured using standard metrics:

* **Precision:** The proportion of correctly extracted entities/relationships among all extracted entities/relationships.  
* **Recall:** The proportion of correctly extracted entities/relationships among all true entities/relationships in the gold standard.  
* **F1-score:** The harmonic mean of precision and recall, providing a balanced measure of accuracy.

The benchmarking process will involve systematic testing of LLMs in various settings, including zero-shot (without examples in the prompt) and few-shot (with a few examples in the prompt) learning.36 The results will inform the selection of the most appropriate LLM models and prompting strategies for the project, balancing accuracy with computational efficiency.

### **Manual Curation Strategies**

Manual curation is an indispensable strategy for verifying and correcting LLM outputs, particularly for complex issues such as incorrect species identification or erroneous substrate pairing. This process is crucial for refining LLM performance over time and ensuring the high quality of the final annotated network.

The project will implement strategies for manual review and correction of LLM-generated extractions. This involves human experts reviewing a subset of LLM outputs against source documents and providing feedback. This feedback loop is essential for identifying systematic errors, refining prompt engineering, and potentially generating additional training data for fine-tuning LLMs if a supervised approach is adopted.

As highlighted in relevant discussions, building custom annotation or curation tools is highly recommended for quick inspection and iterative refinement of LLM-generated data.53 These tools can enable rapid sorting, filtering, and labeling of LLM outputs, making the manual review process more efficient and less laborious. This direct engagement with the data and the model's outputs provides critical qualitative understanding that quantitative metrics alone cannot capture, leading to more targeted improvements in the extraction pipeline. The iterative process of LLM output, manual review, and subsequent refinement of prompts or models is a continuous cycle that drives the improvement of the information extraction system.

## **8\. Conclusions and Recommendations**

This revised plan provides a focused and actionable strategy for developing pure Python stand-alone programs for ontology development and information extraction. By adhering to the specified constraints, the project can still achieve its ambitious goals of enriching plant metabolomics data and deriving deeper biological insights.

The strategic choice of Owlready2 for ontology development and management is critical, as it provides the programmatic capabilities to trim, refine, and semantically enrich existing ontologies. This refinement ensures that the knowledge base is precise and computationally efficient, directly improving the accuracy of downstream LLM-driven information extraction. The emphasis on defining comprehensive hierarchical and semantic relationships within the ontology, coupled with Owlready2's reasoning capabilities, is paramount. This enables the system to move beyond simple data points to infer complex biological connections, which is fundamental for achieving the project's stated objective of "deeper biological insights" into plant metabolite functions.

The core of the project's novelty lies in its LLM-driven information extraction. The detailed plan for corpus building, encompassing PubMed, PMC, and PDFs, with careful consideration for web scraping ethics and bot protection, ensures a rich data source. The multi-faceted approach to Named Entity Recognition (NER) and Relationship Extraction, leveraging fine-tuned BERT models and advanced LLM-based techniques with sophisticated prompt engineering (e.g., LLM-IE, OntoGPT), is essential for accurately extracting structured information from unstructured text.

The subsequent ontology mapping and post-processing steps, utilizing text2term for grounding extracted entities and employing dedupe/recordlinkage for normalization and deduplication, are vital for maintaining data quality and semantic consistency. The integration of multitax and ncbi-taxonomist for robust species identification further enhances the biological accuracy of the network.

Rigorous evaluation and benchmarking, underpinned by the creation of high-quality gold standard test sets and systematic LLM performance measurement, are non-negotiable for validating the system's accuracy. The commitment to manual curation strategies, potentially through custom annotation tools, ensures continuous improvement and refinement of LLM outputs.

**Recommendations:**

1. **Phased Development with Iterative Feedback:** Implement the development plan in distinct phases, with early and frequent integration points. This allows for iterative testing, validation, and refinement of each component, particularly the LLM extraction and ontology mapping pipelines, which are highly dependent on data quality and prompt engineering.  
2. **Dedicated Data Curation Resources:** Allocate significant resources for manual annotation and curation of gold standard datasets and LLM outputs. The quality of this human-labeled data directly dictates the performance and trustworthiness of the LLM models and the overall knowledge network.  
3. **Scalability Planning for LLMs:** While not using API *servers*, consider the computational resources required for LLM inference, especially for large corpora. Plan for efficient local model deployment (e.g., using ollama for open models 1) and optimized batch processing for extraction tasks.32  
4. **Long-Term Maintenance Strategy:** Plan for the long-term maintenance of the ontology and LLM models. This includes strategies for updating ontologies with new biological knowledge, retraining LLMs as new data becomes available, and ensuring the continued compatibility of API integrations with external data sources.  
5. **Clear Documentation for Non-Python Dependencies:** Since Docker is excluded, meticulously document all non-Python system-level dependencies (e.g., Java for Owlready2's reasoner, or any underlying C++ libraries for text2term's mappers) and provide clear installation instructions for users.

By adhering to this comprehensive plan, the project is well-positioned to deliver powerful, reproducible, and highly valuable stand-alone Python programs that will significantly advance our understanding of plant metabolites and their crucial role in plant resilience.