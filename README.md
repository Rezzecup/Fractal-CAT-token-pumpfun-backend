# Fractal-CAT-token-pumpfun-backend
The backend for the Cat Protocol's pump.fun on the Fractal Bitcoin Network utilizes Node.js and sCrypt. Serving as a launchpad for CAT20 tokens, it facilitates the minting, trading, and transferring of CAT20 tokens.  

**Installation:**

```bash
# Clone the repository
git clone https://github.com/Rezzecup/Fractal-CAT-token-pumpfun-backend.git

# Install dependencies
cd Fractal-CAT-token-pumpfun-backend
npm install
```

### Core Functionality

**Token Minting API Fetching:**
```javascript
const { mintToken } = require('./services/tokenService');

(async () => {
  try {
    const result = await mintToken('tokenName', 'tokenSymbol', 1000);
    console.log('Token minted:', result);
  } catch (error) {
    console.error('Error minting token:', error);
  }
})();
```

This function utilizes a service to mint CAT20 tokens, specifying the token details.

**Trading Tokens:**
```javascript
const { tradeToken } = require('./services/tradeService');

(async () => {
  try {
    const result = await tradeToken('tokenId', 'toAddress', 100, 'traderPrivateKey');
    console.log('Token traded:', result);
  } catch (error) {
    console.error('Error trading token:', error);
  }
})();
```

This snippet is a demonstration of trading tokens using a specified trading service.

**Retrieving Token Information:**
```javascript
const fetch = require('node-fetch');

async function getTokenInfo(tokenId) {
  const response = await fetch(`https://api-pump.fun/info?token_id=${tokenId}`, {
    method: 'GET',
    headers: {
      'Content-Type': 'application/json',
      'x-api-key': process.env.API_KEY,
    },
  });
  const data = await response.json();
  return data;
}

(async () => {
  try {
    const info = await getTokenInfo('tokenId');
    console.log('Token Info:', info);
  } catch (error) {
    console.error('Error fetching token info:', error);
  }
})();
```
This example illustrates how to retrieve token metadata using an external API.

Here's a comprehensive guide on structuring the Node.js backend for launching and trading CAT20 tokens on the Fractal Bitcoin Network. This will include examples for the model, controller, and router components using JavaScript and Express.js. 

## Backend Infrastructure Codebase Parts

### 1. Model

The model is responsible for defining the CAT20 token structure and any methods associated with it. Below is an example using a simple class-based approach.

**models/CAT20Token.js:**
```javascript
class CAT20Token {
  constructor(name, symbol, totalSupply, owner) {
    this.name = name;
    this.symbol = symbol;
    this.totalSupply = totalSupply;
    this.owner = owner; // Address of the token owner/creator
    this.balances = { [owner]: totalSupply }; // Track token balances
  }

  transfer(from, to, amount) {
    if (this.balances[from] >= amount) {
      this.balances[from] -= amount;
      if (this.balances[to]) {
        this.balances[to] += amount;
      } else {
        this.balances[to] = amount;
      }
      return true;
    }
    throw new Error('Insufficient balance');
  }
}

module.exports = CAT20Token;
```

### 2. Controller

Controllers handle the business logic and interact with models to process requests. Below is an example of a controller handling token minting and trading.

**controllers/CAT20TokenController.js:**
```javascript
const CAT20Token = require('../models/CAT20Token');

// In-memory storage of tokens for demonstration
const tokens = {};

exports.createToken = (req, res) => {
  const { name, symbol, totalSupply, owner } = req.body;
  if (tokens[symbol]) {
    return res.status(400).json({ message: 'Token symbol already exists' });
  }
  const token = new CAT20Token(name, symbol, totalSupply, owner);
  tokens[symbol] = token;
  res.status(201).json({ message: 'Token created', token });
};

exports.tradeToken = (req, res) => {
  const { symbol, from, to, amount } = req.body;
  const token = tokens[symbol];
  if (!token) {
    return res.status(404).json({ message: 'Token not found' });
  }
  try {
    token.transfer(from, to, amount);
    res.status(200).json({ message: 'Tokens transferred successfully', token });
  } catch (error) {
    res.status(400).json({ message: error.message });
  }
};
```

### 3. Router

The router handles the HTTP requests and is used by the Express app to call the appropriate controller methods.

**routes/CAT20TokenRoutes.js:**
```javascript
const express = require('express');
const router = express.Router();
const CAT20TokenController = require('../controllers/CAT20TokenController');

router.post('/create', CAT20TokenController.createToken);
router.post('/trade', CAT20TokenController.tradeToken);

module.exports = router;
```

### 4. Server Setup

Finally, ensure that these components are tied together within your Express application. 

**server.js:**
```javascript
const express = require('express');
const bodyParser = require('body-parser');
const CAT20TokenRoutes = require('./routes/CAT20TokenRoutes');

const app = express();
app.use(bodyParser.json());

app.use('/api/tokens', CAT20TokenRoutes);

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});
```

### Detailed Explanation

- **Model (`CAT20Token`)**: Represents the CAT20 token structure. This includes properties like `name`, `symbol`, `totalSupply`, and methods for transferring tokens between users.

- **Controller (`CAT20TokenController`)**: Manages the creation and transfer actions. It directly interacts with the token model, managing in-memory token data for simplicity in this example.

- **Router (`CAT20TokenRoutes`)**: Maps incoming HTTP requests to the controller methods, allowing operations for creating and trading tokens via POST requests.


### Contact Information

I've shared some sections of the code, but not the entire codebase due to an NDA and security considerations. If you require technical support or have development inquiries, please feel free to reach out to me.

- Telegram: https://t.me/inscNix/  
