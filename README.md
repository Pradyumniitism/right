give all those files with there names and also make a requirement.txt file

Sure! Below is the complete structure of the project with all the necessary files, including a `requirements.txt` file for managing dependencies.

### Project Structure

```
/supply_chain_project
|-- blockchain.py
|-- supply_chain.py
|-- app.py
|-- templates
    |-- index.html
|-- requirements.txt
```

### File: `blockchain.py`

```python
import hashlib
import json
from time import time

class Block:
    def __init__(self, index, timestamp, transactions, previous_hash):
        self.index = index
        self.timestamp = timestamp
        self.transactions = transactions
        self.previous_hash = previous_hash

    def compute_hash(self):
        block_string = json.dumps(self.__dict__, sort_keys=True).encode()
        return hashlib.sha256(block_string).hexdigest()


class Blockchain:
    def __init__(self):
        self.chain = []
        self.current_transactions = []
        self.create_block(previous_hash='1', proof=100)

    def create_block(self, proof, previous_hash=None):
        block = Block(
            index=len(self.chain) + 1,
            timestamp=time(),
            transactions=self.current_transactions,
            previous_hash=previous_hash or self.chain[-1].compute_hash(),
        )
        self.current_transactions = []  # Reset the current transactions list
        self.chain.append(block)
        return block

    def add_transaction(self, sender, recipient, amount):
        self.current_transactions.append({
            'sender': sender,
            'recipient': recipient,
            'amount': amount,
        })
        return self.last_block.index + 1

    @property
    def last_block(self):
        return self.chain[-1]

    def get_chain(self):
        return [block.__dict__ for block in self.chain]
```

### File: `supply_chain.py`

```python
from blockchain import Blockchain

class SupplyChain:
    def __init__(self):
        self.blockchain = Blockchain()

    def add_product(self, product_id, supplier, retailer):
        # Add transaction to the blockchain
        transaction_index = self.blockchain.add_transaction(supplier, retailer, product_id)
        
        # Create a new block after adding the transaction
        block = self.blockchain.create_block(proof=transaction_index)
        
        print(f"Product {product_id} added to the supply chain.")
        print(f"Block {block.index} has been created.")
    
    def get_supply_chain(self):
        return self.blockchain.get_chain()

if __name__ == "__main__":
    supply_chain = SupplyChain()

    # Simulate adding products to the supply chain
    supply_chain.add_product("Product_001", "Supplier_A", "Retailer_X")
    supply_chain.add_product("Product_002", "Supplier_B", "Retailer_Y")
    
    # Display the entire supply chain (blockchain)
    print("\nCurrent Supply Chain:")
    for block in supply_chain.get_supply_chain():
        print(block)
```

### File: `app.py`

```python
from flask import Flask, render_template, request, redirect, url_for
from blockchain import Blockchain

app = Flask(__name__)
supply_chain = Blockchain()

@app.route('/')
def index():
    return render_template('index.html', chain=supply_chain.get_chain())

@app.route('/add_product', methods=['POST'])
def add_product():
    product_id = request.form['product_id']
    supplier = request.form['supplier']
    retailer = request.form['retailer']
    
    supply_chain.add_transaction(supplier, retailer, product_id)
    supply_chain.create_block(proof=len(supply_chain.chain) + 1)
    
    return redirect(url_for('index'))

if __name__ == "__main__":
    app.run(debug=True)
```

### File: `templates/index.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Supply Chain Blockchain</title>
</head>
<body>
    <h1>Supply Chain Blockchain</h1>
    
    <form action="/add_product" method="post">
        <input type="text" name="product_id" placeholder="Product ID" required>
        <input type="text" name="supplier" placeholder="Supplier" required>
        <input type="text" name="retailer" placeholder="Retailer" required>
        <button type="submit">Add Product</button>
    </form>

    <h2>Current Supply Chain:</h2>
    <ul>
        {% for block in chain %}
            <li>Block {{ block.index }}: {{ block.transactions }}</li>
        {% endfor %}
    </ul>
</body>
</html>
```

### File: `requirements.txt`

```plaintext
Flask==2.0.3
```

### How to Set Up and Run the Project

1. **Create the Project Directory**:
   Create a directory named `supply_chain_project` and navigate into it.

2. **Create Files**:
   Inside this directory, create the following files and folders with their respective content as shown above.

3. **Install Dependencies**:
   Use pip to install Flask by running:

   ```bash
   pip install -r requirements.txt
   ```

4. **Run the Web Application**:
   Start the application by running:

   ```bash
   python app.py
   ```

5. **Access the Application**:
   Open your web browser and go to `http://127.0.0.1:5000/`.

### Conclusion

This structure gives you a foundational project that integrates blockchain technology into a supply chain context with a simple web interface for interaction. You can further enhance it by adding more features or improving existing ones based on your requirements.

