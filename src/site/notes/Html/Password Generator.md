---
{"dg-publish":true,"permalink":"/html/password-generator/","noteIcon":""}
---


Inspired by tools like 1Password, which securely stores and manages passwords, I embarked on a journey to develop a personal password generator. The goal was to create unique, complex passwords for different accounts without the need to remember each one individually. This article outlines the concept and implementation of such a tool using hashing techniques.

---

## The Concept

The idea is to have a single, strong master password (passphrase) that, when combined with a unique identifier for each account (e.g., the website's domain), generates a complex password through hashing. This approach eliminates the need to store passwords, as they can be regenerated on the fly using the same inputs.

---

## Understanding Hashing

Hashing is a process that transforms input data of arbitrary length into a fixed-size string of characters, typically a sequence of numbers and letters. This transformation is performed by a hash function. A key property of hash functions is that they are one-way; it's computationally infeasible to reverse the process and obtain the original input from the hash output. 

Additionally, a good hash function minimizes the chance of two different inputs producing the same output, a situation known as a collision.  

For a deeper dive, refer to the [Wikipedia article on Cryptographic Hash Functions](https://en.wikipedia.org/wiki/Cryptographic_hash_function).

---

## Implementing the Password Generator

To create a predictable and secure password generator, follow these steps:

1. **Input Master Password:**  
   I need an input box which can accept the master password (passphrase in the hashing world).

2. **Combine with a Unique Identifier:**  
   For each type of account, we need to create an Identifier, for time I have made them to be random numbers.

3. **Apply a Hash Function:**  
   Use a cryptographic hash function (e.g., SHA-256) to process the combined string. This will produce a fixed-length hash value.

4. **Format the Output:**  
   Convert the hash output into a suitable format for a password. This may involve encoding the hash and truncating it to the desired length. Ensure the final password includes a mix of uppercase and lowercase letters, numbers, and special characters to meet typical password complexity requirements.

---

## Example in JS

Here's a simple implementation in JS:

```JS
/** This function generates a deterministic 16-character password
* based on a key and identifier
**/
async function generateString2(key, identifier) {

	// Create a seed by combining the key and counter
	const seed = `${key}${identifier}`;
	
	// Generate a SHA-256 hash of the seed
	const hashedSeed = await sha256(seed);
	
	// Define character sets for password requirements
	const specialChars = "@#$^_"; // Special characters to include
	
	const capitalLetters = "ABCDEFGHIJKLMNOPQRSTUVWXYZ"; // Capital letters to include
	const allChars = "abcdefghijklmnopqrstuvwxyz0123456789"+ capitalLetters + specialChars; // All possible chars

	// Select one special char and one capital letter using first 4 bytes of hash as indices	
	const specialChar = specialChars[parseInt(hashedSeed.slice(0, 2), 16) % specialChars.length];
	
	const capitalLetter = capitalLetters[parseInt(hashedSeed.slice(2, 4), 16) % capitalLetters.length];
	
	// Generate remaining characters using subsequent bytes of hash as indices
	const remainingChars = Array.from({ length: 20 }, (_, i) =>
		allChars[parseInt(hashedSeed.slice(4 + i * 2, 6 + i * 2), 16) % allChars.length]);
	
	// Combine special char, capital letter and remaining chars
	const passwordChars = [capitalLetter, specialChar, ...remainingChars];
	
	// Deterministically shuffle the characters using first 8 bytes of hash as seed
	const seedValue = parseInt(hashedSeed.slice(0, 8), 16);
	
	passwordChars.sort(() => (seedValue % 2) - 0.5);

	// Return the first 16 characters joined together	
	return passwordChars.slice(0, 16).join('');

}

function sha256(ascii) {
	const buffer = new TextEncoder("utf-8").encode(ascii);
	return crypto.subtle.digest("SHA-512", buffer).then(hash => {
		return Array.from(new Uint8Array(hash))
					.map(b => b.toString(16).padStart(2, '0'))
					.join('');
		
		}
	);

}
```

---
## Sample application
I created an HTML using Bootstrap and select2, which can take the master key as input, and we can then select any Identifier from the drop-down to generate a password which is 16 characters long, with at least one capital and 1 Special character in it.

```html
<div class="container mt-5">
<h1 class="text-center mb-4">Pass Gen</h1>
<div class="card p-4">
	<div class="form-group">
		<label for="key">Enter the key:</label>
		<input type="text" id="key" name="key" class="form-control">
	</div>
	<div class="form-group">
		<label for="dictKey">Select key for:</label>
		<select id="dictKey" class="form-control">
			<!-- Options will be populated by jQuery -->
		</select>
	</div>
	
	<button id="generate" class="btn btn-primary btn-block">Generate Code</button>
	<p id="result" class="mt-3 text-center"></p>
	</div>
</div>
```

Live app can be found at [Pass Gen](/html/pass-gen)