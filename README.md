

# Email Harvester Integration 



## Step 1: Install theHarvester

First, I installed theHarvester and its dependencies. I made sure I had Python and pip installed, and then ran:

```bash
git clone https://github.com/laramies/theHarvester.git
cd theHarvester
pip install -r requirements.txt
```

## Step 2: Set Up the MERN Stack Application

I created a MERN stack application, which includes an Express backend and a React frontend. If you're starting from scratch, set up the project with the following structure.

## Step 3: Create a Scanning Endpoint in the Backend

In my Express backend, I created an endpoint to execute theHarvester commands. Here's how I set it up:

1. **Set Up Express Server**:

   I ensured my Express server was running and then created a new route to handle the harvesting.

2. **Create the Scan Route**:

   Here’s the code I used to execute theHarvester:

   ```javascript
   const express = require('express');
   const { exec } = require('child_process');
   const cors = require('cors');
   const bodyParser = require('body-parser');

   const app = express();
   app.use(cors());
   app.use(bodyParser.json());

   app.post('/harvest', (req, res) => {
     const { domain } = req.body;

     // Sanitize the input to prevent command injection
     const sanitizedDomain = domain.replace(/[^a-zA-Z0-9.-]/g, '');

     // Execute theHarvester command
     exec(`python3 /path/to/theHarvester/theHarvester.py -d ${sanitizedDomain} -b all`, (error, stdout, stderr) => {
       if (error) {
         console.error(`Error: ${stderr}`);
         return res.status(500).json({ error: stderr });
       }
       res.json({ results: stdout });
     });
   });

   app.listen(5000, () => {
     console.log('Server running on http://localhost:5000');
   });
   ```

   (Make sure to replace `/path/to/theHarvester/theHarvester.py` with the actual path to theHarvester on your system.)

## Step 4: Develop the Frontend

For the frontend, I created a simple form in React to allow users to input a domain and trigger the harvesting process.

```javascript
import React, { useState } from 'react';
import axios from 'axios';

const App = () => {
  const [domain, setDomain] = useState('');
  const [results, setResults] = useState('');

  const handleHarvest = async () => {
    try {
      const response = await axios.post('http://localhost:5000/harvest', { domain });
      setResults(response.data.results);
    } catch (error) {
      setResults('Error: ' + error.message);
    }
  };

  return (
    <div>
      <h1>Email Harvester</h1>
      <input
        type="text"
        value={domain}
        onChange={(e) => setDomain(e.target.value)}
        placeholder="Enter domain to scan"
      />
      <button onClick={handleHarvest}>Harvest Emails</button>
      <pre>{results}</pre>
    </div>
  );
};

export default App;
```

## Step 5: Test the Application

To test the application, I started the Express server:

```bash
node index.js
```

Then, I ran the React app in a separate terminal:

```bash
cd client
npm start
```

Finally, I entered a domain in the form and clicked "Harvest Emails" to see the results.

## Step 6: Security Considerations

- **Input Validation**: It’s crucial to sanitize inputs to prevent command injection attacks.
- **Permission**: Always ensure that I have permission to scan the domain.
- **Error Handling**: Implementing robust error handling helps manage any issues that arise from executing theHarvester.

## Additional Features

In the future, I plan to add:
- **User Authentication**: To track who is using the tool.
- **Save Results**: To store results in a database for later review.
- **Custom Options**: Allowing users to customize the search options for theHarvester.

---
