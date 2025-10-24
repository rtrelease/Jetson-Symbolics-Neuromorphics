## Qwen3-coder (Spark)Ession: Python Owlready2 methods for using SNOWMED-CT and large ontologies with Ollama LLMs

### USER
Can you recommend python methods for using owlready2 and large ontologies like snomed.owl with ollama LLMs?

### ASSISTANT
Here's a comprehensive guide for using owlready2 with large ontologies like SNOMED and integrating with Ollama LLMs:

## Basic Setup and Ontology Loading

```python
import owlready2
import ollama
from owlready2 import *
import pandas as pd
import json

# Load SNOMED ontology
def load_snomed_ontology(ontology_path):
    """Load SNOMED ontology with memory optimization"""
    # Use memory mapping for large ontologies
    owlready2.set_log_level(30)  # WARNING level
    
    # Load with specific configuration for large ontologies
    onto = get_ontology(ontology_path)
    
    # For very large ontologies, consider loading only necessary parts
    onto.load()
    
    return onto

# Example usage
# onto = load_snomed_ontology("snomed.owl")
```

## Memory-Efficient Ontology Processing

```python
class OntologyProcessor:
    def __init__(self, ontology_path):
        self.ontology_path = ontology_path
        self.onto = None
        self.load_ontology()
    
    def load_ontology(self):
        """Load ontology with memory optimization"""
        # Set memory limits
        owlready2.set_log_level(30)
        
        # Load with specific settings
        self.onto = get_ontology(self.ontology_path)
        
        # For large ontologies, consider incremental loading
        # or loading only specific modules
        self.onto.load()
    
    def get_concept_hierarchy(self, concept_name, max_depth=3):
        """Get concept hierarchy with depth limiting"""
        try:
            concept = self.onto.search_one(label=concept_name)
            if not concept:
                return None
                
            hierarchy = []
            current_level = [concept]
            depth = 0
            
            while current_level and depth < max_depth:
                next_level = []
                for item in current_level:
                    # Get direct parents
                    parents = list(item.is_a)
                    hierarchy.append({
                        'concept': str(item),
                        'parents': [str(p) for p in parents],
                        'depth': depth
                    })
                    next_level.extend(parents)
                
                current_level = next_level
                depth += 1
                
            return hierarchy
        except Exception as e:
            print(f"Error getting hierarchy: {e}")
            return None
    
    def search_concepts(self, query, limit=10):
        """Search for concepts with fuzzy matching"""
        try:
            # Use owlready2's built-in search
            results = list(self.onto.search(label=query))
            return [{'label': str(r), 'iri': str(r.iri)} for r in results[:limit]]
        except Exception as e:
            print(f"Search error: {e}")
            return []
```

## Integration with Ollama LLMs

```python
class OntologyLLMInterface:
    def __init__(self, model_name="llama3"):
        self.model_name = model_name
        self.processor = None
    
    def set_processor(self, processor):
        """Set the ontology processor"""
        self.processor = processor
    
    def generate_concept_description(self, concept_name, context=""):
        """Generate description using LLM with ontology context"""
        
        # Get concept information
        concept_info = self.processor.get_concept_hierarchy(concept_name, max_depth=2)
        
        # Prepare prompt
        prompt = f"""
        You are an expert medical terminology assistant.
        
        Context: {context}
        
        Concept: {concept_name}
        
        Ontology Information:
        {json.dumps(concept_info, indent=2) if concept_info else 'No information available'}
        
        Please provide a clear, concise explanation of this medical concept.
        """
        
        try:
            response = ollama.chat(
                model=self.model_name,
                messages=[{
                    'role': 'user',
                    'content': prompt
                }],
                options={
                    'temperature': 0.3,
                    'max_tokens': 500
                }
            )
            
            return response['message']['content']
        except Exception as e:
            print(f"LLM error: {e}")
            return "Error generating response"
    
    def answer_medical_question(self, question, concept_context=None):
        """Answer medical questions using both ontology and LLM"""
        
        # First, search for relevant concepts
        if concept_context:
            concept_results = self.processor.search_concepts(concept_context)
        else:
            concept_results = []
        
        # Create comprehensive prompt
        prompt = f"""
        You are a medical expert assistant with access to SNOMED CT ontology.
        
        Question: {question}
        
        Relevant Concepts Found:
        {json.dumps(concept_results, indent=2) if concept_results else 'None found'}
        
        Please provide a well-reasoned, medically accurate response.
        """
        
        try:
            response = ollama.chat(
                model=self.model_name,
                messages=[{
                    'role': 'user',
                    'content': prompt
                }],
                options={
                    'temperature': 0.2,
                    'max_tokens': 1000
                }
            )
            
            return response['message']['content']
        except Exception as e:
            print(f"LLM error: {e}")
            return "Error processing question"
```

## Optimized Querying and Caching

```python
import hashlib
import pickle
from functools import lru_cache

class CachedOntologyProcessor(OntologyProcessor):
    def __init__(self, ontology_path, cache_size=100):
        super().__init__(ontology_path)
        self.cache_size = cache_size
        self.query_cache = {}
    
    @lru_cache(maxsize=100)
    def cached_search(self, query, limit=10):
        """Cached search for frequently used queries"""
        return self.search_concepts(query, limit)
    
    def get_concept_details(self, concept_name):
        """Get detailed concept information with caching"""
        
        # Create cache key
        cache_key = hashlib.md5(concept_name.encode()).hexdigest()
        
        if cache_key in self.query_cache:
            return self.query_cache[cache_key]
        
        try:
            concept = self.onto.search_one(label=concept_name)
            if not concept:
                return None
            
            details = {
                'label': str(concept),
                'iri': str(concept.iri),
                'parents': [str(p) for p in concept.is_a],
                'children': [str(c) for c in concept.subclasses()],
                'properties': [(p, str(v)) for p, v in concept.get_properties()]
            }
            
            # Cache the result
            self.query_cache[cache_key] = details
            return details
            
        except Exception as e:
            print(f"Error getting concept details: {e}")
            return None
```

## Practical Usage Examples

```python
# Initialize components
def main():
    # Load ontology (adjust path as needed)
    # onto = load_snomed_ontology("path/to/snomed.owl")
    
    # For demonstration, create a simple processor
    processor = CachedOntologyProcessor("path/to/snomed.owl")
    
    # Initialize LLM interface
    llm_interface = OntologyLLMInterface("llama3")
    llm_interface.set_processor(processor)
    
    # Example 1: Get concept description
    description = llm_interface.generate_concept_description(
        "Myocardial infarction", 
        "Cardiovascular disease context"
    )
    print("Concept Description:", description)
    
    # Example 2: Answer medical question
    answer = llm_interface.answer_medical_question(
        "What are the symptoms of heart attack?",
        "heart attack"
    )
    print("Question Answer:", answer)
    
    # Example 3: Search and explore
    concepts = processor.search_concepts("diabetes")
    print("Related concepts:", concepts)

# Batch processing for multiple queries
def batch_process_queries(queries, processor, model_name="llama3"):
    """Process multiple queries efficiently"""
    
    llm_interface = OntologyLLMInterface(model_name)
    llm_interface.set_processor(processor)
    
    results = []
    for query in queries:
        try:
            answer = llm_interface.answer_medical_question(query)
            results.append({
                'query': query,
                'answer': answer
            })
        except Exception as e:
            print(f"Error processing {query}: {e}")
            results.append({
                'query': query,
                'error': str(e)
            })
    
    return results

# Example usage
if __name__ == "__main__":
    # Sample queries
    sample_queries = [
        "What is hypertension?",
        "Symptoms of diabetes mellitus",
        "Treatment options for asthma"
    ]
    
    # Process batch
    # results = batch_process_queries(sample_queries, processor)
    # for result in results:
    #     print(f"Query: {result['query']}")
    #     print(f"Answer: {result['answer']}\n")
```

## Performance Optimization Tips

```python
# Memory optimization settings
def optimize_ontology_loading():
    """Optimize ontology loading for large ontologies"""
    
    # Set memory limits
    owlready2.set_log_level(30)  # Reduce logging
    
    # Consider using ontologies in parts
    # Only load necessary modules
    
    # For very large ontologies, consider:
    # 1. Loading only specific ontologies
    # 2. Using SPARQL queries instead of full ontology loading
    # 3. Implementing lazy loading
    
    pass

# Connection pooling for multiple queries
class LLMConnectionPool:
    def __init__(self, model_name="llama3", pool_size=3):
        self.model_name = model_name
        self.pool_size = pool_size
        self.connections = []
        
    def get_connection(self):
        """Get a connection (in practice, this would be connection pooling)"""
        return ollama
```

## Key Recommendations:

1. **Memory Management**: Use `lru_cache` and implement caching for repeated queries
2. **Selective Loading**: Load only necessary parts of large ontologies
3. **Batch Processing**: Process multiple queries in batches to reduce overhead
4. **Error Handling**: Implement robust error handling for both ontology and LLM operations
5. **Context Management**: Pass relevant ontology context to LLMs for better responses
6. **Temperature Control**: Use lower temperatures (0.2-0.3) for factual questions

This approach balances the memory requirements of large ontologies with the capabilities of local LLMs through efficient caching and selective processing.

### USER
What are some best practices for handling errors when the Ollama LLM is unavailable or returns unexpected responses?

### ASSISTANT
Here are comprehensive best practices for handling errors when working with Ollama LLMs:

## Robust Error Handling Framework

```python
import time
import logging
from typing import Optional, Dict, Any
from dataclasses import dataclass
import json
from functools import wraps

# Configure logging
logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

@dataclass
class LLMResponse:
    """Structured response class for LLM interactions"""
    success: bool
    content: Optional[str] = None
    error: Optional[str] = None
    response_time: Optional[float] = None
    metadata: Optional[Dict] = None

class OllamaErrorHandler:
    """Comprehensive error handling for Ollama interactions"""
    
    def __init__(self, max_retries: int = 3, retry_delay: float = 1.0):
        self.max_retries = max_retries
        self.retry_delay = retry_delay
        self.error_counts = {}
    
    def handle_ollama_call(self, func):
        """Decorator for handling Ollama calls with retry logic"""
        @wraps(func)
        def wrapper(*args, **kwargs):
            for attempt in range(self.max_retries):
                try:
                    start_time = time.time()
                    result = func(*args, **kwargs)
                    response_time = time.time() - start_time
                    
                    # Validate response structure
                    if not self._is_valid_response(result):
                        raise ValueError("Invalid response structure from Ollama")
                    
                    return LLMResponse(
                        success=True,
                        content=result.get('message', {}).get('content', ''),
                        response_time=response_time,
                        metadata={'attempt': attempt + 1}
                    )
                    
                except Exception as e:
                    error_msg = str(e)
                    logger.warning(f"Ollama call attempt {attempt + 1} failed: {error_msg}")
                    
                    # Count errors
                    self.error_counts[error_msg] = self.error_counts.get(error_msg, 0) + 1
                    
                    # Don't retry on certain critical errors
                    if self._is_critical_error(e):
                        logger.error(f"Critical error - not retrying: {error_msg}")
                        return LLMResponse(
                            success=False,
                            error=error_msg,
                            metadata={'attempt': attempt + 1, 'critical': True}
                        )
                    
                    # Wait before retry
                    if attempt < self.max_retries - 1:
                        time.sleep(self.retry_delay * (2 ** attempt))  # Exponential backoff
                    else:
                        logger.error(f"All retry attempts failed for {func.__name__}")
                        return LLMResponse(
                            success=False,
                            error=f"Failed after {self.max_retries} attempts: {error_msg}",
                            metadata={'attempt': attempt + 1}
                        )
            
            return LLMResponse(success=False, error="Unknown error")
        return wrapper
    
    def _is_valid_response(self, response: Dict) -> bool:
        """Validate that response has expected structure"""
        if not isinstance(response, dict):
            return False
        
        if 'message' not in response:
            return False
        
        message = response.get('message', {})
        if not isinstance(message, dict):
            return False
        
        if 'content' not in message:
            return False
            
        return True
    
    def _is_critical_error(self, exception: Exception) -> bool:
        """Determine if error should not be retried"""
        error_str = str(exception).lower()
        
        critical_errors = [
            'connection refused',
            'no such container',
            'ollama is not running',
            'server closed the connection',
            'timeout',
            'network error'
        ]
        
        return any(critical in error_str for critical in critical_errors)
    
    def get_error_statistics(self) -> Dict:
        """Get statistics about error occurrences"""
        return self.error_counts.copy()
    
    def reset_error_counts(self):
        """Reset error counters"""
        self.error_counts.clear()
```

## Fallback Strategies

```python
class FallbackLLMInterface:
    """Interface with multiple fallback strategies"""
    
    def __init__(self, primary_model: str = "llama3", fallback_models: list = None):
        self.primary_model = primary_model
        self.fallback_models = fallback_models or ["mistral", "phi3"]
        self.error_handler = OllamaErrorHandler()
        self.cache = {}
        
    def get_fallback_model(self, current_model: str) -> str:
        """Get next available fallback model"""
        if current_model in self.fallback_models:
            index = self.fallback_models.index(current_model)
            if index < len(self.fallback_models) - 1:
                return self.fallback_models[index + 1]
        return self.fallback_models[0] if self.fallback_models else self.primary_model
    
    def safe_ollama_call(self, model: str, messages: list, **options) -> LLMResponse:
        """Make Ollama call with comprehensive error handling"""
        
        @self.error_handler.handle_ollama_call
        def _call_ollama(model_name, msg_list, opts):
            return ollama.chat(
                model=model_name,
                messages=msg_list,
                options=opts
            )
        
        try:
            response = _call_ollama(model, messages, options)
            
            # If primary fails, try fallback models
            if not response.success:
                logger.info(f"Primary model {model} failed, trying fallback models")
                return self._try_fallback_models(messages, options)
            
            return response
            
        except Exception as e:
            logger.error(f"Unexpected error in safe_ollama_call: {e}")
            return LLMResponse(
                success=False,
                error=f"Unexpected error: {str(e)}"
            )
    
    def _try_fallback_models(self, messages: list, options: dict) -> LLMResponse:
        """Try fallback models sequentially"""
        for model in self.fallback_models:
            logger.info(f"Trying fallback model: {model}")
            try:
                response = ollama.chat(
                    model=model,
                    messages=messages,
                    options=options
                )
                
                if response.get('message', {}).get('content'):
                    logger.info(f"Successfully used fallback model: {model}")
                    return LLMResponse(
                        success=True,
                        content=response['message']['content'],
                        metadata={'fallback_used': model}
                    )
                    
            except Exception as e:
                logger.warning(f"Fallback model {model} also failed: {e}")
                continue
        
        return LLMResponse(
            success=False,
            error="All models failed"
        )
    
    def get_cached_or_fallback(self, cache_key: str, query_func) -> LLMResponse:
        """Get response from cache or fallback mechanism"""
        # Try cache first
        if cache_key in self.cache:
            cached_response = self.cache[cache_key]
            if time.time() - cached_response.get('timestamp', 0) < 3600:  # 1 hour
                logger.info("Using cached response")
                return LLMResponse(
                    success=True,
                    content=cached_response['content'],
                    metadata={'cached': True}
                )
        
        # If no cache or expired, get fresh response
        response = query_func()
        
        # Cache successful responses
        if response.success:
            self.cache[cache_key] = {
                'content': response.content,
                'timestamp': time.time()
            }
        
        return response
```

## Graceful Degradation Patterns

```python
class GracefulDegradationInterface:
    """Interface that gracefully degrades when LLM is unavailable"""
    
    def __init__(self, ontology_processor, fallback_strategy="simple"):
        self.ontology_processor = ontology_processor
        self.fallback_strategy = fallback_strategy
        self.last_successful_response = None
        self.is_ollama_available = True
        self.availability_check_count = 0
        self.availability_threshold = 5  # Check every 5 calls
        
    def query_with_fallback(self, question: str, context: str = "") -> LLMResponse:
        """Main query method with fallback logic"""
        
        # Periodic availability check
        self.availability_check_count += 1
        if self.availability_check_count >= self.availability_threshold:
            self._check_ollama_availability()
            self.availability_check_count = 0
        
        # If Ollama is unavailable, use fallback
        if not self.is_ollama_available:
            logger.warning("Ollama unavailable, using fallback strategy")
            return self._apply_fallback_strategy(question, context)
        
        # Normal operation
        return self._normal_query(question, context)
    
    def _check_ollama_availability(self):
        """Check if Ollama is available"""
        try:
            # Simple health check
            ollama.list()  # This will raise exception if not available
            self.is_ollama_available = True
            logger.info("Ollama is available")
        except Exception as e:
            self.is_ollama_available = False
            logger.warning(f"Ollama is not available: {e}")
    
    def _normal_query(self, question: str, context: str) -> LLMResponse:
        """Normal query to Ollama"""
        try:
            # Prepare context-aware prompt
            prompt = self._build_contextual_prompt(question, context)
            
            response = ollama.chat(
                model="llama3",
                messages=[{"role": "user", "content": prompt}],
                options={"temperature": 0.3}
            )
            
            self.last_successful_response = response
            
            return LLMResponse(
                success=True,
                content=response['message']['content']
            )
            
        except Exception as e:
            logger.error(f"Ollama query failed: {e}")
            return LLMResponse(
                success=False,
                error=f"Ollama query failed: {str(e)}"
            )
    
    def _apply_fallback_strategy(self, question: str, context: str) -> LLMResponse:
        """Apply fallback strategy based on configuration"""
        try:
            if self.fallback_strategy == "simple":
                return self._simple_fallback(question, context)
            elif self.fallback_strategy == "ontology":
                return self._ontology_fallback(question, context)
            elif self.fallback_strategy == "hybrid":
                return self._hybrid_fallback(question, context)
            else:
                return self._simple_fallback(question, context)
                
        except Exception as e:
            logger.error(f"Fallback strategy failed: {e}")
            return LLMResponse(
                success=False,
                error=f"Fallback failed: {str(e)}",
                metadata={'fallback_failed': True}
            )
    
    def _simple_fallback(self, question: str, context: str) -> LLMResponse:
        """Simple fallback - return basic response"""
        return LLMResponse(
            success=True,
            content=f"Based on my knowledge, I can't provide a detailed answer to '{question}' at the moment. "
                   f"This is a fallback response due to LLM unavailability."
        )
    
    def _ontology_fallback(self, question: str, context: str) -> LLMResponse:
        """Fallback using ontology data"""
        try:
            # Extract key terms from question
            key_terms = self._extract_key_terms(question)
            
            # Search ontology for relevant information
            ontology_results = self.ontology_processor.search(key_terms)
            
            if ontology_results:
                return LLMResponse(
                    success=True,
                    content=f"Based on available information: {ontology_results[:200]}..."
                )
            else:
                return self._simple_fallback(question, context)
                
        except Exception as e:
            logger.error(f"Ontology fallback failed: {e}")
            return self._simple_fallback(question, context)
    
    def _hybrid_fallback(self, question: str, context: str) -> LLMResponse:
        """Hybrid fallback - combine multiple strategies"""
        # Try last successful response
        if self.last_successful_response:
            return LLMResponse(
                success=True,
                content=f"Previous response: {self.last_successful_response['message']['content']}"
            )
        
        # Fallback to simple response
        return self._simple_fallback(question, context)
    
    def _build_contextual_prompt(self, question: str, context: str) -> str:
        """Build a contextual prompt for better responses"""
        if context:
            return f"Context: {context}\nQuestion: {question}"
        return question
    
    def _extract_key_terms(self, text: str) -> list:
        """Simple key term extraction"""
        # This would be more sophisticated in practice
        return [word for word in text.lower().split() if len(word) > 3]
```

## Comprehensive Usage Example

```python
# Initialize components
ontology_processor = YourOntologyProcessor()  # Your existing processor
fallback_interface = FallbackLLMInterface()
graceful_interface = GracefulDegradationInterface(ontology_processor)

def robust_query_system(question: str, context: str = "") -> str:
    """Main query function with comprehensive error handling"""
    
    try:
        # Get response with fallback
        response = graceful_interface.query_with_fallback(question, context)
        
        if response.success:
            logger.info("Query successful")
            return response.content
        else:
            logger.error(f"Query failed: {response.error}")
            
            # Log error statistics
            error_handler = OllamaErrorHandler()
            logger.info(f"Error statistics: {error_handler.get_error_statistics()}")
            
            # Return error message or fallback content
            return f"Error processing request: {response.error}"
            
    except Exception as e:
        logger.critical(f"Critical error in query system: {e}")
        return "System error occurred. Please try again later."

# Usage examples
if __name__ == "__main__":
    # Test normal operation
    result = robust_query_system("What is machine learning?")
    print(result)
    
    # Test with context
    result = robust_query_system("Explain neural networks", "Technical context")
    print(result)
    
    # Test error handling
    error_result = robust_query_system("This will fail")
    print(error_result)
```

## Key Best Practices Summary:

1. **Exponential Backoff**: Implement progressive retry delays
2. **Multiple Fallbacks**: Have several fallback strategies (simple, ontology, hybrid)
3. **Health Checks**: Periodically verify LLM availability
4. **Caching**: Cache successful responses to reduce dependency on LLM
5. **Structured Logging**: Log all errors and retry attempts for debugging
6. **Error Statistics**: Track error patterns to identify issues
7. **Graceful Degradation**: Provide meaningful responses even when LLM fails
8. **Timeout Handling**: Set reasonable timeouts for all network calls
9. **Context Preservation**: Maintain context even during fallbacks
10. **Monitoring**: Implement monitoring for LLM availability and performance

This approach ensures your system remains functional and provides meaningful responses even when the LLM is unavailable or returns unexpected responses.
