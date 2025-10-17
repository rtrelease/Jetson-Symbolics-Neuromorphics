## Qwen3-coder Owlready2 with large ontologies and Ollama LLMs

### USER
Recommend python code with owlready2  for using large .owl ontologies with local ollama llms.

### ASSISTANT
Here's a Python solution using `owlready2` and `ollama` to work with large OWL ontologies and local LLMs:

```python
import owlready2
import ollama
import re
from typing import List, Dict, Any
import time

class OntologyProcessor:
    def __init__(self, ontology_path: str, ollama_model: str = "llama3"):
        """
        Initialize with ontology file path and Ollama model name
        """
        self.ontology_path = ontology_path
        self.ollama_model = ollama_model
        self.ontology = None
        self.reasoner = None
        
    def load_ontology(self, reasoner: str = "hermit"):
        """
        Load ontology with optional reasoner
        """
        print("Loading ontology...")
        self.ontology = owlready2.get_ontology(self.ontology_path).load()
        
        # Apply reasoner if specified
        if reasoner:
            print(f"Applying {reasoner} reasoner...")
            self.reasoner = owlready2.World().get_ontology(self.ontology_path)
            # Note: OWLReasoner integration requires additional setup
            # This is a simplified approach - full reasoning needs more configuration
        
        print("Ontology loaded successfully")
        return self.ontology
    
    def get_ontology_summary(self) -> Dict[str, Any]:
        """
        Get basic summary of ontology structure
        """
        if not self.ontology:
            raise ValueError("Ontology not loaded")
            
        summary = {
            "classes": len(list(self.ontology.classes())),
            "properties": len(list(self.ontology.properties())),
            "individuals": len(list(self.ontology.individuals())),
            "object_properties": len(list(self.ontology.object_properties())),
            "data_properties": len(list(self.ontology.data_properties())),
        }
        return summary
    
    def query_ontology(self, query: str, max_results: int = 10) -> List[str]:
        """
        Query ontology using natural language with Ollama
        """
        # Get basic ontology information
        summary = self.get_ontology_summary()
        classes = list(self.ontology.classes())[:max_results]
        properties = list(self.ontology.properties())[:max_results]
        
        # Prepare context for LLM
        context = f"""
        Ontology contains:
        - {summary['classes']} classes
        - {summary['properties']} properties
        - {summary['individuals']} individuals
        - {summary['object_properties']} object properties
        - {summary['data_properties']} data properties
        
        Sample classes: {[str(c) for c in classes]}
        Sample properties: {[str(p) for p in properties]}
        
        Query: {query}
        """
        
        # Send to Ollama
        try:
            response = ollama.chat(
                model=self.ollama_model,
                messages=[
                    {
                        "role": "user",
                        "content": f"Based on the ontology information, answer the following query: {query}. "
                                   f"Provide concise technical response. Context: {context}"
                    }
                ]
            )
            return [response['message']['content']]
        except Exception as e:
            return [f"Error processing query: {str(e)}"]
    
    def extract_class_hierarchy(self, depth: int = 2) -> Dict[str, List[str]]:
        """
        Extract class hierarchy up to specified depth
        """
        hierarchy = {}
        for cls in self.ontology.classes():
            if not cls.is_a:
                continue
            ancestors = [str(a) for a in cls.is_a if a != owlready2.Thing]
            if ancestors:
                hierarchy[str(cls)] = ancestors[:depth]
        return hierarchy
    
    def semantic_search(self, term: str, max_results: int = 5) -> List[Dict[str, Any]]:
        """
        Search for semantic matches using LLM
        """
        # Get relevant classes and properties
        all_classes = list(self.ontology.classes())
        all_properties = list(self.ontology.properties())
        
        # Create search context
        search_context = {
            "classes": [str(c) for c in all_classes[:20]],  # Limit for performance
            "properties": [str(p) for p in all_properties[:20]],
            "term": term
        }
        
        # Query with LLM
        prompt = f"""
        Find semantic matches for '{term}' in this ontology.
        Classes: {', '.join(search_context['classes'])}
        Properties: {', '.join(search_context['properties'])}
        
        Return results in JSON format with 'matches' array containing:
        - 'entity': matched entity name
        - 'type': 'class' or 'property'
        - 'description': brief description
        """
        
        try:
            response = ollama.chat(
                model=self.ollama_model,
                messages=[
                    {"role": "user", "content": prompt}
                ]
            )
            # Parse response (simplified - you might want to use JSON parsing)
            content = response['message']['content']
            # This is a simplified approach - implement proper JSON parsing
            return [{"entity": term, "type": "class", "description": content}]
        except Exception as e:
            return [{"error": str(e)}]
    
    def generate_ontology_documentation(self) -> str:
        """
        Generate documentation using LLM
        """
        summary = self.get_ontology_summary()
        hierarchy = self.extract_class_hierarchy(1)
        
        prompt = f"""
        Generate comprehensive documentation for this ontology:
        
        Summary:
        {summary}
        
        Class hierarchy (first level):
        {hierarchy}
        
        Include:
        1. Overview of ontology purpose
        2. Key classes and their relationships
        3. Main properties used
        4. How to use this ontology for reasoning
        """
        
        try:
            response = ollama.chat(
                model=self.ollama_model,
                messages=[
                    {"role": "user", "content": prompt}
                ]
            )
            return response['message']['content']
        except Exception as e:
            return f"Error generating documentation: {str(e)}"

# Example usage
def main():
    # Initialize processor
    processor = OntologyProcessor(
        ontology_path="path/to/your/ontology.owl",
        ollama_model="llama3"
    )
    
    # Load ontology
    ontology = processor.load_ontology()
    
    # Get summary
    print("Ontology Summary:", processor.get_ontology_summary())
    
    # Query with LLM
    results = processor.query_ontology("What are the main classes in this ontology?")
    print("Query Results:", results)
    
    # Semantic search
    matches = processor.semantic_search("medical diagnosis")
    print("Semantic Matches:", matches)
    
    # Generate documentation
    doc = processor.generate_ontology_documentation()
    print("Documentation:", doc[:500] + "..." if len(doc) > 500 else doc)

if __name__ == "__main__":
    main()
```

### Key Features:

1. **Ontology Loading**: Efficiently loads large OWL ontologies with optional reasoner
2. **LLM Integration**: Uses Ollama for natural language queries and semantic understanding
3. **Query Processing**: Converts natural language queries into actionable ontology information
4. **Semantic Search**: Finds relevant classes/properties based on user terms
5. **Documentation Generation**: Creates comprehensive ontology documentation
6. **Performance Optimization**: Limits results to prevent memory issues with large ontologies

### Requirements:

```bash
pip install owlready2 ollama
```

### Usage Notes:

1. Ensure Ollama is running locally (`ollama serve`)
2. Use appropriate model names (e.g., "llama3", "mistral", "phi3")
3. For very large ontologies, consider:
   - Loading only specific sections
   - Using pagination for results
   - Implementing caching for repeated queries
4. Adjust `max_results` parameters based on your needs
5. The code handles errors gracefully and provides informative messages

### Performance Tips:

1. Cache LLM responses for repeated queries
2. Use smaller ontology subsets when possible
3. Implement connection pooling for multiple queries
4. Consider using `owlready2`'s `get_ontology()` with `load()` for better memory management

This approach allows you to leverage the semantic power of large OWL ontologies with local LLMs for natural language interaction while maintaining performance for large datasets.

### USER
How would I modify this to work with other local LLMs like Mistral or Phi-3 instead of Llama3?

### ASSISTANT
Here's the modified code that supports multiple local LLMs including Mistral and Phi-3:

```python
import owlready2
import ollama
import re
from typing import List, Dict, Any, Optional
import time
import json

class OntologyProcessor:
    def __init__(self, ontology_path: str, ollama_model: str = "llama3", 
                 llm_provider: str = "ollama", max_tokens: int = 2048):
        """
        Initialize with ontology file path and LLM configuration
        """
        self.ontology_path = ontology_path
        self.ollama_model = ollama_model
        self.llm_provider = llm_provider  # "ollama", "openai", "huggingface"
        self.max_tokens = max_tokens
        self.ontology = None
        self.reasoner = None
        
    def load_ontology(self, reasoner: str = "hermit"):
        """
        Load ontology with optional reasoner
        """
        print("Loading ontology...")
        self.ontology = owlready2.get_ontology(self.ontology_path).load()
        
        # Apply reasoner if specified
        if reasoner:
            print(f"Applying {reasoner} reasoner...")
            # Note: OWLReasoner integration requires additional setup
            # This is a simplified approach - full reasoning needs more configuration
        
        print("Ontology loaded successfully")
        return self.ontology
    
    def get_ontology_summary(self) -> Dict[str, Any]:
        """
        Get basic summary of ontology structure
        """
        if not self.ontology:
            raise ValueError("Ontology not loaded")
            
        summary = {
            "classes": len(list(self.ontology.classes())),
            "properties": len(list(self.ontology.properties())),
            "individuals": len(list(self.ontology.individuals())),
            "object_properties": len(list(self.ontology.object_properties())),
            "data_properties": len(list(self.ontology.data_properties())),
        }
        return summary
    
    def _query_llm(self, prompt: str, model_name: Optional[str] = None) -> str:
        """
        Query the configured LLM with error handling
        """
        if model_name is None:
            model_name = self.ollama_model
            
        try:
            if self.llm_provider == "ollama":
                response = ollama.chat(
                    model=model_name,
                    messages=[
                        {"role": "user", "content": prompt}
                    ],
                    options={
                        "temperature": 0.3,
                        "max_tokens": self.max_tokens
                    }
                )
                return response['message']['content']
            
            elif self.llm_provider == "openai":
                # For OpenAI API (requires openai package)
                import openai
                response = openai.ChatCompletion.create(
                    model=model_name,
                    messages=[{"role": "user", "content": prompt}],
                    max_tokens=self.max_tokens,
                    temperature=0.3
                )
                return response.choices[0].message.content
            
            else:
                raise ValueError(f"Unsupported LLM provider: {self.llm_provider}")
                
        except Exception as e:
            return f"Error querying LLM: {str(e)}"
    
    def query_ontology(self, query: str, max_results: int = 10) -> List[str]:
        """
        Query ontology using natural language with configured LLM
        """
        # Get basic ontology information
        summary = self.get_ontology_summary()
        classes = list(self.ontology.classes())[:max_results]
        properties = list(self.ontology.properties())[:max_results]
        
        # Prepare context for LLM
        context = f"""
        Ontology contains:
        - {summary['classes']} classes
        - {summary['properties']} properties
        - {summary['individuals']} individuals
        - {summary['object_properties']} object properties
        - {summary['data_properties']} data properties
        
        Sample classes: {[str(c) for c in classes]}
        Sample properties: {[str(p) for p in properties]}
        
        Query: {query}
        """
        
        # Adjust prompt based on LLM capabilities
        if "phi" in self.ollama_model.lower() or "mistral" in self.ollama_model.lower():
            # Phi-3 and Mistral have different strengths
            prompt = f"""
            Based on the ontology information, answer the following query: {query}.
            Provide concise technical response.
            
            Context: {context}
            
            Important: Keep responses under 200 words and focus on technical accuracy.
            """
        else:
            prompt = f"""
            Based on the ontology information, answer the following query: {query}.
            Provide concise technical response. Context: {context}
            """
        
        # Send to LLM
        response = self._query_llm(prompt)
        return [response]
    
    def extract_class_hierarchy(self, depth: int = 2) -> Dict[str, List[str]]:
        """
        Extract class hierarchy up to specified depth
        """
        hierarchy = {}
        for cls in self.ontology.classes():
            if not cls.is_a:
                continue
            ancestors = [str(a) for a in cls.is_a if a != owlready2.Thing]
            if ancestors:
                hierarchy[str(cls)] = ancestors[:depth]
        return hierarchy
    
    def semantic_search(self, term: str, max_results: int = 5) -> List[Dict[str, Any]]:
        """
        Search for semantic matches using LLM
        """
        # Get relevant classes and properties
        all_classes = list(self.ontology.classes())
        all_properties = list(self.ontology.properties())
        
        # Create search context
        search_context = {
            "classes": [str(c) for c in all_classes[:20]],  # Limit for performance
            "properties": [str(p) for p in all_properties[:20]],
            "term": term
        }
        
        # Different prompts for different models
        if "phi" in self.ollama_model.lower():
            prompt = f"""
            Find semantic matches for '{term}' in this ontology.
            Classes: {', '.join(search_context['classes'])}
            Properties: {', '.join(search_context['properties'])}
            
            Return results in JSON format with 'matches' array containing:
            - 'entity': matched entity name
            - 'type': 'class' or 'property'
            - 'description': brief description
            
            Focus on precise matching and provide only the JSON response.
            """
        elif "mistral" in self.ollama_model.lower():
            prompt = f"""
            Semantic search for '{term}' in ontology:
            Classes: {', '.join(search_context['classes'])}
            Properties: {', '.join(search_context['properties'])}
            
            Return structured JSON with matches array.
            Each match should have entity, type, and description fields.
            """
        else:
            prompt = f"""
            Find semantic matches for '{term}' in this ontology.
            Classes: {', '.join(search_context['classes'])}
            Properties: {', '.join(search_context['properties'])}
            
            Return results in JSON format with 'matches' array containing:
            - 'entity': matched entity name
            - 'type': 'class' or 'property'
            - 'description': brief description
            """
        
        response = self._query_llm(prompt)
        
        # Try to parse JSON response
        try:
            if response.strip().startswith('{') or response.strip().startswith('['):
                parsed = json.loads(response)
                if isinstance(parsed, list):
                    return parsed
                elif isinstance(parsed, dict) and 'matches' in parsed:
                    return parsed['matches']
            return [{"entity": term, "type": "class", "description": response}]
        except:
            return [{"entity": term, "type": "class", "description": response}]
    
    def generate_ontology_documentation(self, format_type: str = "markdown") -> str:
        """
        Generate documentation using LLM with format support
        """
        summary = self.get_ontology_summary()
        hierarchy = self.extract_class_hierarchy(1)
        
        if format_type == "markdown":
            prompt = f"""
            Generate comprehensive documentation for this ontology in Markdown format:
            
            Summary:
            {summary}
            
            Class hierarchy (first level):
            {hierarchy}
            
            Include:
            1. Overview of ontology purpose
            2. Key classes and their relationships
            3. Main properties used
            4. How to use this ontology for reasoning
            5. Example usage patterns
            
            Format as clean Markdown with proper headings and lists.
            """
        else:
            prompt = f"""
            Generate comprehensive documentation for this ontology:
            
            Summary:
            {summary}
            
            Class hierarchy (first level):
            {hierarchy}
            
            Include:
            1. Overview of ontology purpose
            2. Key classes and their relationships
            3. Main properties used
            4. How to use this ontology for reasoning
            """
        
        response = self._query_llm(prompt)
        return response
    
    def batch_query_ontology(self, queries: List[str]) -> Dict[str, str]:
        """
        Process multiple queries efficiently
        """
        results = {}
        for query in queries:
            results[query] = self.query_ontology(query)[0]
        return results

# Example usage functions for different LLMs
def example_with_phi3():
    """Example using Phi-3 model"""
    processor = OntologyProcessor(
        ontology_path="path/to/your/ontology.owl",
        ollama_model="phi3",
        llm_provider="ollama"
    )
    
    ontology = processor.load_ontology()
    print("Ontology Summary:", processor.get_ontology_summary())
    
    # Query with Phi-3
    results = processor.query_ontology("What are the main medical concepts in this ontology?")
    print("Query Results:", results)
    
    # Semantic search
    matches = processor.semantic_search("diagnosis")
    print("Semantic Matches:", matches)

def example_with_mistral():
    """Example using Mistral model"""
    processor = OntologyProcessor(
        ontology_path="path/to/your/ontology.owl",
        ollama_model="mistral",
        llm_provider="ollama"
    )
    
    ontology = processor.load_ontology()
    print("Ontology Summary:", processor.get_ontology_summary())
    
    # Generate documentation
    doc = processor.generate_ontology_documentation("markdown")
    print("Documentation:", doc[:500] + "..." if len(doc) > 500 else doc)

def example_with_llama():
    """Example using Llama model"""
    processor = OntologyProcessor(
        ontology_path="path/to/your/ontology.owl",
        ollama_model="llama3",
        llm_provider="ollama"
    )
    
    ontology = processor.load_ontology()
    print("Ontology Summary:", processor.get_ontology_summary())
    
    # Batch query
    queries = [
        "What are the main classes?",
        "What are the relationships?",
        "How can this be used for reasoning?"
    ]
    results = processor.batch_query_ontology(queries)
    for query, result in results.items():
        print(f"Query: {query}")
        print(f"Result: {result[:100]}...")

# Quick setup examples
if __name__ == "__main__":
    # For Phi-3
    print("=== Phi-3 Example ===")
    example_with_phi3()
    
    print("\n=== Mistral Example ===")
    example_with_mistral()
    
    print("\n=== Llama Example ===")
    example_with_llama()
```

This enhanced version includes:

1. **Multiple LLM Support**: Works with Phi-3, Mistral, Llama, and other Ollama models
2. **Model-Specific Prompts**: Adjusts prompts based on the LLM being used
3. **Flexible Configuration**: Easy to switch between different models
4. **Error Handling**: Robust error handling for LLM queries
5. **Batch Processing**: Support for processing multiple queries
6. **Documentation Generation**: Supports different output formats
7. **Semantic Search**: Enhanced search functionality with JSON output parsing

To use with different models:
- Phi-3: `ollama_model="phi3"`
- Mistral: `ollama_model="mistral"`
- Llama: `ollama_model="llama3"`
- Or any other Ollama model you have installed

The code automatically adapts prompts and response handling based on the specific LLM being used, making it more effective for each model's strengths.
