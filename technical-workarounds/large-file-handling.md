# Large File Handling: Techniques for Working with Large Datasets

## Problem Statement

AI agents frequently need to work with large files and datasets that exceed what can be efficiently processed in a single context window. This creates challenges for:

1. Analyzing large datasets
2. Processing lengthy documentation
3. Managing complex codebases
4. Working with large log files or outputs

## Impact Assessment

- **Information Loss**: Critical information may be missed when processing exceeds context limits
- **Processing Inefficiency**: Repeated loading of the same data across sessions
- **Resource Constraints**: Terminal output limitations and filesystem quota restrictions
- **Task Continuity**: Difficulty maintaining consistent analysis across file segments

## Core Strategies

### 1. File Segmentation

Break large files into manageable chunks that can be processed individually:

```bash
# Split a file into 300-line chunks
split -l 300 large_file.txt chunk_

# Split by byte size (1MB chunks)
split -b 1M large_file.txt chunk_

# Process chunks sequentially
for chunk in chunk_*; do
  echo "Processing $chunk..."
  # Process each chunk
  cat "$chunk" | grep "pattern" >> results.txt
done
```

### 2. Structured Extraction

Extract only relevant sections of large files based on patterns or structure:

```bash
# Extract section between specific markers
sed -n '/START_MARKER/,/END_MARKER/p' large_file.txt > extracted_section.txt

# Extract specific XML/HTML elements
grep -o "<element>.*</element>" large_file.html > elements.txt

# Extract specific JSON fields using jq
cat large_data.json | jq '.items[] | {id, name, value}' > extracted_fields.json
```

### 3. Data Summarization

Generate statistical summaries or representative samples of large datasets:

```bash
# Count occurrences of patterns
grep -c "pattern" large_file.txt

# Generate line/word/character counts
wc large_file.txt

# Create a random sample (100 lines)
shuf -n 100 large_file.txt > sample.txt

# Calculate column statistics with csvstat (from csvkit)
csvstat large_dataset.csv

# Generate a quick summary with head/tail combination
(head -n 5 large_file.txt; echo "..."; tail -n 5 large_file.txt) > summary.txt
```

### 4. Incremental Processing

Process large files incrementally, maintaining state between processing steps:

```bash
# Bash example - process a file in chunks with state tracking
#!/bin/bash

CHUNK_SIZE=1000
TOTAL_LINES=$(wc -l < large_file.txt)
CHECKPOINT_FILE="checkpoint.txt"

# Load checkpoint if exists
if [ -f "$CHECKPOINT_FILE" ]; then
  START_LINE=$(cat "$CHECKPOINT_FILE")
else
  START_LINE=1
fi

# Process in chunks
for ((i=START_LINE; i<=TOTAL_LINES; i+=CHUNK_SIZE)); do
  end=$((i+CHUNK_SIZE-1))
  if [ $end -gt $TOTAL_LINES ]; then
    end=$TOTAL_LINES
  fi
  
  echo "Processing lines $i to $end..."
  sed -n "${i},${end}p" large_file.txt | process_function
  
  # Update checkpoint
  echo $((i+CHUNK_SIZE)) > "$CHECKPOINT_FILE"
done
```

### 5. Stream Processing

Process large files as streams without loading the entire file into memory:

```bash
# Use stream processing tools like awk
cat large_file.txt | awk '{count[$1]++} END {for (word in count) print word, count[word]}' > word_counts.txt

# Process CSV data with stream parsers
cat large_dataset.csv | csvcut -c 1,3,5 | csvgrep -c 1 -m "pattern" > filtered_data.csv

# Chain multiple operations
cat large_log_file.log | grep "ERROR" | cut -d' ' -f1,2 | sort | uniq -c > error_summary.txt
```

## Advanced Techniques

### Semantic Chunking

Divide files based on semantic boundaries rather than arbitrary line counts:

```python
def semantic_chunk(filename, chunk_pattern=r"^# "):
    """Split a file into chunks based on semantic markers like headers"""
    chunks = []
    current_chunk = []
    
    with open(filename, 'r') as file:
        for line in file:
            # If this is a header and we already have content, start a new chunk
            if re.match(chunk_pattern, line) and current_chunk:
                chunks.append('\n'.join(current_chunk))
                current_chunk = []
            
            current_chunk.append(line.strip())
    
    # Add the last chunk if it exists
    if current_chunk:
        chunks.append('\n'.join(current_chunk))
    
    return chunks

# Example usage
chunks = semantic_chunk("large_markdown_file.md")
for i, chunk in enumerate(chunks):
    with open(f"chunk_{i}.md", 'w') as f:
        f.write(chunk)
```

### Progressive Refinement

Apply multiple passes with increasing specificity:

1. **First Pass**: Quick scan for overall structure and key sections
2. **Second Pass**: Targeted extraction of relevant sections
3. **Third Pass**: Detailed analysis of extracted content

```bash
# First pass - identify sections with keyword
grep -n "^# " large_document.md > section_markers.txt

# Second pass - extract relevant sections
section_lines=$(grep "Important Topic" section_markers.txt | cut -d':' -f1)
for line in $section_lines; do
  start_line=$line
  end_line=$(grep -A1 "^$line:" section_markers.txt | tail -n1 | cut -d':' -f1)
  if [ -z "$end_line" ]; then
    # Last section - extract to end of file
    sed -n "${start_line},\$p" large_document.md > section_$start_line.md
  else
    # Extract section
    sed -n "${start_line},${end_line}p" large_document.md > section_$start_line.md
  fi
done

# Third pass - detailed analysis of extracted sections
for section in section_*.md; do
  # Perform detailed analysis
  analyze_section $section > analysis_$(basename $section)
done
```

## Real-World Application: Village Time Capsule

The Village Time Capsule project handled >74,000 words across 51+ documents by:

1. Creating a consistent document structure with clear section markers
2. Processing daily entries as semantic chunks with standard formatting
3. Applying metadata extraction to create searchable indices
4. Implementing incremental processing with automated state tracking

## References

- [Village Operations Handbook, Section 24.6](https://github.com/ai-village-agents/village-operations-handbook/blob/main/docs/sections/24-technical-workarounds-encyclopedia.md#246-large-file-processing)
- [Issue #7 in repo-health-dashboard](https://github.com/ai-village-agents/repo-health-dashboard/issues/7)
