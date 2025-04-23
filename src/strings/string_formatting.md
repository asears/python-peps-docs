## t-strings
- **Definition**: t-strings are a type of string formatting that allows for the inclusion of variables and expressions within a string.
- **Origin**: Template strings (t-strings) were popularized by the `string.Template` class introduced in Python 2.4 through [PEP 292](https://peps.python.org/pep-0292/). They were designed as a simpler, safer alternative to % formatting.
- **Syntax**: They are typically implemented using the `string.Template` class in Python's standard library. While some libraries might use a `t` prefix, this is not a standard Python feature.
- **Use Case**: t-strings are useful when you want to create a string that includes dynamic content with a simpler, more restricted syntax than f-strings or % formatting.

:::code-group
```python [Template String Example]
from string import Template

# Basic template string
name = "Alice"
age = 30
template = Template("My name is $name and I am $age years old")
result = template.substitute(name=name, age=age)
print(result)  # Output: My name is Alice and I am 30 years old

# Template with dictionary
data = {'item': 'book', 'price': 29.99}
template = Template("The $item costs $$price")
result = template.substitute(data)
print(result)  # Output: The book costs $29.99
```
:::

## f-strings
- **Definition**: f-strings, or formatted string literals, provide a way to embed expressions inside string literals, using curly braces `{}`.
- **Origin**: f-strings were introduced in Python 3.6 through [PEP 498](https://peps.python.org/pep-0498/) and have become the preferred way of string formatting in modern Python. They were designed to make string interpolation more readable and reduce the cognitive overhead of string formatting.
- **Syntax**: An f-string is defined by prefixing the string with the letter `f` or `F`. Expressions inside curly braces are evaluated at runtime.
- **Use Case**: f-strings are preferred for their simplicity and readability. They allow for inline expressions and can include any valid Python expression, making them very versatile.

:::code-group
```python [Basic f-string Examples]
name = "Alice"
age = 30

# Basic variable interpolation
greeting = f"Hello, {name}!"
print(greeting)  # Output: Hello, Alice!

# Expressions in f-strings
result = f"In 5 years, {name} will be {age + 5} years old"
print(result)  # Output: In 5 years, Alice will be 35 years old

# Format specifiers
price = 42.7589
formatted = f"The price is ${price:.2f}"
print(formatted)  # Output: The price is $42.76
```

```python [Advanced f-string Features]
# Dictionary access
data = {'name': 'Bob', 'score': 95}
result = f"{data['name']} scored {data['score']}%"
print(result)  # Output: Bob scored 95%

# Method calls
text = "python"
result = f"Uppercase: {text.upper()}"
print(result)  # Output: Uppercase: PYTHON

# Date formatting
from datetime import datetime
now = datetime.now()
result = f"Current date: {now:%Y-%m-%d}"
print(result)  # Output: Current date: 2025-04-22
```
:::

## When to Use Each
- **t-strings**: Use template strings when:
  - You need a safer string formatting approach that prevents code injection
  - You're handling user-supplied format strings
  - You want to ensure only simple substitutions are possible
  - You need to separate the template definition from the data
- **f-strings**: Use f-strings when:
  - You want the most readable and concise syntax
  - You need to embed complex expressions or function calls
  - You're working with Python 3.6 or later
  - You need access to advanced formatting options
  - Performance is a priority (f-strings are generally faster)

## Logging Patterns and String Formatting
- **Why Avoid f-strings in Logging**: While f-strings are powerful and convenient, they should be avoided in logging for several reasons:
  - F-strings are eagerly evaluated, meaning expressions are computed even if the log level is not enabled
  - This can lead to unnecessary performance overhead, especially with expensive operations
  - Template strings or %-formatting provide lazy evaluation, only computing values when needed
  - Better memory usage as string formatting only occurs for logs that will actually be written

:::code-group
```python [Logging Best Practices]
import logging

# Configure logging
logging.basicConfig(level=logging.DEBUG)
logger = logging.getLogger(__name__)

# Bad practice - f-strings are always evaluated
user_data = {'name': 'Alice', 'items': list(range(10000))}
logger.debug(f"Processing user data: {user_data}")  # Expensive string formatting happens even if debug is disabled

# Good practice - lazy evaluation
logger.debug("Processing user data: %s", user_data)  # String formatting only happens if debug is enabled

# Example for each log level
def process_order(order):
    # Debug - detailed information for debugging
    logger.debug("Processing order details: %s", order)
    
    # Info - confirmation that things are working as expected
    logger.info("Order %s received from customer %s", order.id, order.customer)
    
    try:
        if not order.items:
            # Warning - something unexpected but not critical
            logger.warning("Order %s received with no items", order.id)
            return
            
        if order.total > 1000:
            # Error - something failed but application can continue
            logger.error("Order %s exceeds maximum allowed amount: $%.2f", 
                        order.id, order.total)
            raise ValueError("Order amount too high")
            
    except Exception as e:
        # Critical - application failure requiring immediate attention
        logger.critical("Fatal error processing order %s: %s", 
                       order.id, str(e), exc_info=True)
        # Re-raise the exception after logging
        raise
```

```python [Exception Handling Patterns]
import logging
from typing import Dict, Any

logger = logging.getLogger(__name__)

class OrderProcessingError(Exception):
    """Custom exception for order processing errors"""
    pass

def validate_order(order_data: Dict[str, Any]) -> bool:
    try:
        # Validation logic here
        if not isinstance(order_data.get('total'), (int, float)):
            msg = "Invalid order total"
            logger.error("%s: %s", msg, order_data)
            raise OrderProcessingError(msg)
            
        if not order_data.get('items'):
            logger.warning("Order received with no items: %s", order_data['id'])
            return False
            
    except KeyError as e:
        # Log with exception information
        logger.error("Missing required field: %s", str(e), exc_info=True)
        # Wrap the exception with custom context
        raise OrderProcessingError(f"Invalid order data: missing {str(e)}") from e
        
    except Exception as e:
        # Log unexpected errors
        logger.critical(
            "Unexpected error validating order: %s", 
            str(e), 
            exc_info=True
        )
        # Re-raise unexpected exceptions
        raise
        
    return True
```
:::

### Logging Best Practices
1. **Use Lazy Evaluation**
   - Prefer `logger.debug("Message: %s", value)` over `logger.debug(f"Message: {value}")`
   - Use %-formatting or template strings instead of f-strings
   
2. **Include Context**
   - Log relevant data that helps diagnose issues
   - Include transaction IDs, user IDs, or other correlation identifiers
   
3. **Choose Appropriate Log Levels**
   - DEBUG: Detailed information for debugging
   - INFO: Confirmation that things are working as expected
   - WARNING: Something unexpected but not critical
   - ERROR: Something failed but application can continue
   - CRITICAL: Application failure requiring immediate attention
   
4. **Exception Handling**
   - Always include `exc_info=True` when logging exceptions
   - Use custom exceptions for different error categories
   - Log before re-raising exceptions
   - Consider using `raise ... from ...` to preserve exception context

5. **Performance Considerations**
   - Use logging level checks for expensive operations
   - Guard expensive debug logging:
     ```python
     if logger.isEnabledFor(logging.DEBUG):
         logger.debug("Expensive operation result: %s", expensive_function())
     ```

## Measuring Logging Memory Usage
Understanding the memory impact of different string formatting approaches in logging is crucial for performance-critical applications. Here's how to measure and compare memory usage:

:::code-group
```python [Memory Profiling Example]
"""Memory profiling examples for string formatting in logging.

Alternative libraries:
- arrow: Modern datetime alternative
- pendulum: Production-ready datetime library
- freezegun: Time mocking for tests
- python-dateutil: Extended datetime functionality
"""
from memory_profiler import profile
import logging
import sys
from typing import list, dict
from datetime import datetime as dt

# Error messages as constants
ERR_MEMORY_THRESHOLD = "Memory usage exceeded threshold: {usage} MB"
ERR_INVALID_DATA = "Invalid data format: {detail}"

logger = logging.getLogger(__name__)

@profile
def log_with_fstring(data: list[dict]) -> None:
    """Log data using f-strings (eager evaluation).
    
    Args:
        data: List of dictionaries containing test data
        
    Note:
        This demonstrates higher memory usage due to eager evaluation
    """
    for item in data:
        logger.debug(f"Processing complex data structure: {item}")

@profile
def log_with_percent(data: list[dict]) -> None:
    """Log data using %-formatting (lazy evaluation).
    
    Args:
        data: List of dictionaries containing test data
        
    Note:
        This demonstrates lower memory usage due to lazy evaluation
    """
    for item in data:
        logger.debug("Processing complex data structure: %s", item)

# Test data
test_data = [{"id": i, "values": list(range(1000))} for i in range(1000)]

if __name__ == "__main__":
    logging.basicConfig(level=logging.ERROR)
    print("Memory usage comparison:")
    log_with_fstring(test_data)
    log_with_percent(test_data)
```

```python [Pytest Memory Tests]
"""Pytest-based memory testing for logging string formatting.

Required pytest plugins:
- pytest-memray: Advanced memory profiling
- pytest-memprof: Memory leak detection
- pytest-benchmark: Performance benchmarking
- pytest-monitor: Resource usage monitoring

Alternative threading libraries:
- concurrent.futures: Higher-level threading/processing
- asyncio: Async/await pattern
- trio: Structured concurrency
- gevent: Coroutine-based Python networking
"""
import pytest
import logging
import gc
from typing import callable, any
import sys
import psutil
from datetime import datetime as dt
import tracemalloc
from pytest_benchmark.fixture import BenchmarkFixture

# Fixture for memory monitoring
@pytest.fixture
def memory_tracker():
    """Fixture for tracking memory usage in tests.
    
    Yields:
        callable: Function to measure memory usage
        
    Example:
        def test_memory(memory_tracker):
            usage = memory_tracker(my_function)
            assert usage < threshold
    """
    tracemalloc.start()
    yield lambda func: _measure_memory(func)
    tracemalloc.stop()

def _measure_memory(func: callable) -> int:
    """Internal function to measure memory usage.
    
    Args:
        func: Function to measure memory usage for
        
    Returns:
        int: Peak memory usage in bytes
    """
    gc.collect()
    snapshot1 = tracemalloc.take_snapshot()
    func()
    snapshot2 = tracemalloc.take_snapshot()
    stats = snapshot2.compare_to(snapshot1, 'lineno')
    return sum(stat.size_diff for stat in stats)

def test_string_formatting_memory(memory_tracker):
    """Test memory usage of different string formatting approaches.
    
    Args:
        memory_tracker: Pytest fixture for memory tracking
        
    Note:
        This test compares memory usage between f-strings and %-formatting
    """
    # Test data
    large_dict = {
        "data": list(range(10000)),
        "nested": {"more_data": [i * 2 for i in range(1000)]}
    }
    
    def log_with_fstring():
        logger.debug(f"Processing: {large_dict}")
    
    def log_with_percent():
        logger.debug("Processing: %s", large_dict)
    
    f_memory = memory_tracker(log_with_fstring)
    p_memory = memory_tracker(log_with_percent)
    
    assert p_memory < f_memory, (
        f"%-formatting ({p_memory:,} bytes) should use less memory "
        f"than f-strings ({f_memory:,} bytes)"
    )

def test_logging_performance(benchmark: BenchmarkFixture):
    """Benchmark performance of different logging approaches.
    
    Args:
        benchmark: Pytest-benchmark fixture
        
    Note:
        Run with: pytest --benchmark-only
    """
    data = {"complex": list(range(1000))}
    
    def f_string_log():
        logger.debug(f"Data: {data}")
    
    def percent_log():
        logger.debug("Data: %s", data)
    
    # Compare performance
    f_result = benchmark.pedantic(
        f_string_log,
        iterations=100,
        rounds=100
    )
    
    p_result = benchmark.pedantic(
        percent_log,
        iterations=100,
        rounds=100
    )
    
    # Optional: Compare results
    assert p_result.stats.mean < f_result.stats.mean

# Example conftest.py content for memory testing:
"""
import pytest

def pytest_addoption(parser):
    parser.addoption(
        "--memory-threshold",
        type=int,
        default=100,
        help="Maximum allowed memory increase in MB"
    )

@pytest.fixture
def memory_threshold(request):
    return request.config.getoption("--memory-threshold")
"""
```

```python [Production Memory Monitor]
"""Production memory monitoring with advanced features.

Alternative monitoring libraries:
- prometheus_client: Metrics collection
- statsd: Stats aggregation
- datadog: Full monitoring solution
- newrelic: Application performance monitoring
"""
import psutil
import logging
from typing import dict, optional
from datetime import datetime as dt
import threading
from threading import Event
import time

class MemoryMonitor:
    """Monitor memory usage over time with thread-safe implementation."""
    
    def __init__(self, interval: int = 60):
        """Initialize memory monitor.
        
        Args:
            interval: Seconds between memory checks (default: 60)
            
        Note:
            Uses threading.Event for cleaner shutdown
        """
        self.interval = interval
        self._stop_event = Event()
        self.stats: dict[dt, float] = {}
        self.logger = logging.getLogger(__name__)
        self._lock = threading.Lock()
        
    def start(self) -> None:
        """Start monitoring memory usage in daemon thread."""
        self._stop_event.clear()
        threading.Thread(
            target=self._monitor,
            daemon=True,
            name="memory-monitor"
        ).start()
        
    def stop(self) -> None:
        """Stop monitoring memory usage."""
        self._stop_event.set()
        
    def _monitor(self) -> None:
        """Monitor memory usage in separate thread.
        
        Note:
            Uses process.memory_full_info() for detailed memory stats
        """
        process = psutil.Process()
        while not self._stop_event.is_set():
            try:
                memory = process.memory_full_info()
                with self._lock:
                    self.stats[dt.now()] = {
                        'rss': memory.rss / 1024 / 1024,  # MB
                        'vms': memory.vms / 1024 / 1024,
                        'shared': memory.shared / 1024 / 1024
                    }
            except psutil.Error as e:
                self.logger.error("Memory monitoring error: %s", str(e))
            
            self._stop_event.wait(self.interval)
            
    def get_memory_increase(self, minutes: int = 60) -> optional[dict[str, float]]:
        """Calculate memory increase over specified period.
        
        Args:
            minutes: Time period to check in minutes (default: 60)
            
        Returns:
            dict: Memory changes by type (rss, vms, shared) or None
            
        Note:
            Thread-safe implementation with proper locking
        """
        with self._lock:
            if len(self.stats) < 2:
                return None
                
            times = sorted(self.stats.keys())
            start_time = times[0]
            end_time = times[-1]
            
            if (end_time - start_time).total_seconds() < minutes * 60:
                return None
                
            return {
                'rss': self.stats[end_time]['rss'] - self.stats[start_time]['rss'],
                'vms': self.stats[end_time]['vms'] - self.stats[start_time]['vms'],
                'shared': self.stats[end_time]['shared'] - self.stats[start_time]['shared']
            }

# Usage example
monitor = MemoryMonitor(interval=30)
monitor.start()

try:
    # Your application code here
    large_data = [range(1000) for _ in range(1000)]
    
    # Bad practice - f-string with large data structure
    logger.debug(f"Processing data: {large_data}")
    
    # Good practice - lazy evaluation
    logger.debug("Processing data: %s", large_data)
    
    # Check memory increase after an hour
    increase = monitor.get_memory_increase(minutes=60)
    if increase and increase['rss'] > 100:  # More than 100MB RSS increase
        logger.warning(
            "Memory usage increased - RSS: %.2f MB, VMS: %.2f MB",
            increase['rss'],
            increase['vms']
        )
        
finally:
    monitor.stop()
```
:::

### Memory Testing Best Practices
1. **Use Pytest Plugins**
   - `pytest-memray`: Detailed memory analysis
   - `pytest-benchmark`: Performance testing
   - `pytest-monitor`: Resource usage tracking
   - `pytest-memprof`: Memory leak detection
   - `pytest-timeout`: Prevent memory-heavy tests from hanging
   
2. **Testing Strategy**
   - Use fixtures for consistent test setup
   - Implement parametrized tests for different data sizes
   - Include memory thresholds in CI/CD pipeline
   - Monitor trends over time with pytest-benchmark
   
3. **Production Monitoring**
   - Use proper thread synchronization
   - Implement graceful shutdown
   - Monitor multiple memory metrics (RSS, VMS, shared)
   - Consider using APM tools (New Relic, Datadog)
   
4. **Performance Tips**
   ```python
   # Using pytest-benchmark for performance monitoring
   @pytest.mark.benchmark(
       min_rounds=100,
       warmup=True,
       warmup_iterations=10
   )
   def test_logging_performance(benchmark):
       benchmark(your_logging_function)
   ```