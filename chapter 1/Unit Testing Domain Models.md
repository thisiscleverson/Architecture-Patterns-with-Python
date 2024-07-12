# Unit Testing Domain Models



Test describes the behavior that we want to see from the system. The test enables us to see if our business logic works correctly. 

**see an example:**

``` python
from typing import Optional
from dataclasses import dataclass
from datetime import date

@dataclass(frozen=True)
class OrderLine:
    orderid: str
    sku: str
    qty: int

class Batch:
    def __init__(self, ref: str, sku: str, qty: int, eta:Optional[date]):
        self.reference = ref
        self.sku = sku
        self.eta = eta
        self.available_quantity = qty

    def allocate(self, line: OrderLine):
        self.available_quantity -= line.qty

```

This code illustrates one batch, which has a method `allocate` to decrement the value of `available_quantity`. This represents the number of available vacancies in an batch.

We can create a test to test this code. See an example:

```python
import pytest
from datetime import date

from model import *

def test_allocating_to_a_batch_reduces_the_available_quantity():
    batch = Batch("batch-001", "SMALL-TABLE", qty=20,eta=date.today())
    line  = OrderLine('order-ref', "SMALL-TABLE", 2)
    batch.allocate(line)
    
    assert batch.available_quantity == 18

```

See we created one test to reduces the available quantity in a batch. This test enable us check if I decrement 2 of 20 allocation, the values is 18.

##   

But this code is trivial: a `Batch` just wraps an integer available_quantity, and we decrement that value on allocation. We can created more methods to class `Batch`, a example is one method check if can allocate a batch.

**see example:**

Model.py

```python
@dataclass(frozen=True)
class OrderLine:
    orderid: str
    sku: str
    qty: int

class Batch:
    def __init__(self, ref: str, sku: str, qty: int, eta:Optional[date]):
        self.reference = ref
        self.sku = sku
        self.eta = eta
        self.available_quantity = qty

    def allocate(self, line: OrderLine):
        self.available_quantity -= line.qty
	
    def can_allocate(self, line: OrderLine) -> bool:
		return self.sku == line.sku and self.available_quantity >= line.qty
```

test_batches.py

```python
import pytest
from datetime import date

from model import *

def make_batch_and_line(sku, batch_qty, line_qty):
    return (
        Batch("batch-001", sku, batch_qty,eta=date.today()),
        OrderLine("order-123", sku, line_qty)
    )

def test_allocating_to_a_batch_reduces_the_available_quantity():
    batch = Batch("batch-001", "SMALL-TABLE", qty=20,eta=date.today())
    line = OrderLine('order-ref', "SMALL-TABLE", 2)
    batch.allocate(line)

    assert batch.available_quantity == 18

def test_can_allocate_if_available_equal_to_required():
    batch, line = make_batch_and_line("ELEGANT-LAMP", 2, 2)
    
    assert batch.can_allocate(line) is True

def test_cannot_allocate_if_skus_do_not_match():
    batch = Batch("batch-001", "UNCOMFORTABLE-CHAIR", 100, eta=None)
    different_sku_line = OrderLine("order-123", "EXPENSIVE-TOASTER", 10)
    
    assert batch.can_allocate(different_sku_line) is False

```

