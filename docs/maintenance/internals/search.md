# Search

The following summarizes key concepts about search in InvenioRDM. It both summarizes some of the documentation found in OpenSearch's own documentation and highlights how it relates to the internals of InvenioRDM.

## Key Considerations for Customizing Index Mappings

### Size

- Field Type Selection: Use lightweight field types (e.g., keyword over text where appropriate) to minimize index size.
- Usage of custom analyzers or filters should be as limited as possible to prevent index bloating.

### Speed

- Search Performance: Keeping size in mind, apply custom analyzers that include `edge_ngram` filter to provide quick suggestions, and optimize for frequently queried fields to enhance search speed.
- Analyzer and filter selection: Configure only when necessary to improve search time.

## Fine tuning the search

Boosting affects the relevance score of documents. A higher boost value means a stronger influence on the search ranking. Determine which fields are most critical for your search relevance (e.g., titles, authors, keywords).

- **Relevance Adjustment:** Boosting of field(s) can be done by using the caret operator **(^)** followed by a number. For example:
    * `name^100` will boost the name field by a factor of 100.
    * Asterisk **(\*)** can be used to apply boosting to all the subfields. `i18n_titles.*^50`

- **Balance and Tuning:** Use boosting judiciously to avoid skewing results too heavily towards particular fields. Assign boost factors based on the importance of each field. Higher values increase the influence of matches in that field.

Tuning search where multiple fields are searched upon is essential to make sure that relevant results are always returned first. Taking the affiliations index as an example, the key fields are `name`, `acronym` and `title.{subfields}`. Since affiliations are usually searched by name, name is given more weight to boost its relevance.

```
"name^80", "acronym^40", "title.*^20"
```

## Analyzers

Analyzers allow for searches to match documents which are not exact matches. For example, matching different cases, without accents, parts of words, mis-spellings, etc. Fundamentally all analyzers must contain one tokenizer. A tokenizer essentially splits an input search into parts. Additionally an analyzer may optionally have one or many character filters and/or token filters.

- A **character filter** is applied first and takes the entire input and adds, removes or changes any characters depending on our needs.
- The [**tokenizer**](https://opensearch.org/docs/latest/analyzers/tokenizers/index/) then splits the input into parts (words)
- Finally the [**token filter**](https://opensearch.org/docs/latest/analyzers/token-filters/index/) acts similarly to a tokenizer, but is applied to each input "word"

Read more about analyzers on [the OpenSearch official docs](https://opensearch.org/docs/latest/analyzers/).

Analyzers can be applied to both the search input and when the document is indexed. In most cases we want to apply the same analyzer to the search input and during indexing so that there is not unexpected behaviour.

- [**Normalizers**](https://opensearch.org/docs/latest/analyzers/normalizers/) — Simpler and mainly used to improve the matching of keyword search. The `keyword` type is the simplest way in which data can be stored and by default works as an exact match search. Using a normalizer you can add, remove and alter the input into exactly one other token which is stored and searched for.

## Character filters

Character filters take the stream of characters before tokenization and can add, remove or replace characters according to the rules and type of filter. There are 3 types - mapping filter, pattern replace filter and HTML stripping filter.

For our indices in InvenioRDM, we are currently using a custom pattern replace filter that uses regex to remove special characters!

```
"char_filter": {
  "strip_special_chars": {
    "type": "pattern_replace",
    "pattern": "[\\p{Punct}\\p{S}]",
    "replacement": ""
  }
}
```

## Tokenizers and token filters

We are using the following tokenizers and token filters in some of our indices in InvenioRDM:

- **[ngram and edge_ngram](https://opensearch.org/docs/latest/analyzers/tokenizers/index/#partial-word-tokenizers)** — Both of these are used for matching parts of words, n-gram creates n sized chunks ("car" ngram(1,2) -> "c", "ca", "a", "ar") and edge_ngram creates chunks from the beginning of the word ("dog" edge_ngram(1,3) -> "d", "do", "dog"). Edge N-gram enables prefix searching and is preferred as it produces less tokens. Additionally it is recommended that these are used as token filters so that they produce tokens on each word rather than between words.
- **[uax_url_email](https://opensearch.org/docs/latest/analyzers/tokenizers/index/#word-tokenizers)** — If it is likely that searches and/or documents will contain URLs or emails, it is better to use this tokenizer. If a standard tokenizer is used the URL/email will be split on the special characters which results in behaviour which may be unexpected (searching tim@apple.com will return documents with apple in them for example)
- **[asciifolding](https://opensearch.org/docs/latest/analyzers/token-filters/index/)** — Allows characters to match with many different representations, especially relevant for non-English languages. For example ä -> a, Å -> A, etc.
