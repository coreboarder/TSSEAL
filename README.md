<!-- @format -->

# TSSEAL Usage Guide

## Overview

TSSEAL is a TypeScript-based library designed for advanced encryption utilities using homomorphic encryption leveraging SEAL. This guide explains how to:

1. Install the library.
2. Enable debugging.
3. Use it effectively in an Angular project.

TSSEAL enables computations on encrypted data directly producing encrypted results that, when decrypted, match the output of operations performed on plaintext. It is efficient for polynomial arithmetic and modular arithmetic implementations.
Currently it only upports BFV (Brakerski/Fan-Vercauteren) but is roadmapped to support CKKS (Cheon-Kim-Kim-Song) encryption schemes. Compliant with security standards such as IND-CPA (Indistinguishability under Chosen Plaintext Attack).

SEAL’s encryption schemes rely on the Ring Learning with Errors (RLWE) problem, which is a lattice-based cryptographic problem. This problem is believed to be difficult to solve, even for quantum computers, making it a strong foundation for security.

**NOTE:** The actual encrypted value is stored as polynomial coefficients within the CipherText object. These coefficients are not directly exposed in a view but are managed internally within any instance.

## Applications:

- Privacy-preserving machine learning.
- Secure data analysis.
- Federated learning.
- Encrypted database queries.

---

## Demo

You can access a functional demo here: [https://coreboarder.github.io/TSSEAL/](https://coreboarder.github.io/TSSEAL/)

## Installation

To install the library, use the following command:

```
npm install tsseal
```

The package is available on [https://www.npmjs.com/package/tsseal](https://www.npmjs.com/package/tsseal).

## Component Setup

### Import

Import the library with

```
import { TSSEAL } from 'tsseal';
```

### Initialization

The library has to be initialized prior to calls being made. To do so do the following

```
export class AppComponent implements OnInit {
  sealLib: TSSEAL;

  constructor() {
    this.sealLib = new TSSEAL();
  }

  ngOnInit() {
    this.initializeLibrary();
  }

  async initializeLibrary(): Promise<void> {
    await this.sealLib.initializeLibrary();
    console.log('SEAL Library initialized successfully');
  }
```

## Debugging

Basic console logging is enable by appending an option boolean set to true. Check the examples below for more.

## Examples

### Encrypt a single number

The encrypt function takes a number as input, converts it into a format that can be securely encrypted, and then encrypts it. This encrypted data can then be safely stored, transmitted, or used for computations without exposing the original value.

```
<button (click)="testEncryption()">Test Encryption</button>

async testEncryption() {
    console.clear();
    const input = 1234;
    console.log('Input:', input);
    const encrypted = await this.encrypt(input);
    console.log('Encrypted:', encrypted);
    const decrypted = await this.decrypt(encrypted);
    console.log('Decrypted:', decrypted);
    }

    async encrypt(input: number): Promise<any> {
    return this.sealLib.encrypt(input, true);
}
```

### Add multiple numbers together

```
<button (click)="testAddition()">Test Addition</button>

async testAddition() {
    console.clear();
    const inputs = [1, 0, 0, 4, 5]; // Example array of numbers
    console.log('Inputs:', inputs);
    // Encrypt all inputs
    const encryptedInputs = [];
    for (const input of inputs) {
        encryptedInputs.push(await this.encrypt(input));
    }

    // Add all ciphertexts
    const sum = await this.sealLib.addCiphertexts(encryptedInputs, true);

    console.log('Sum:', sum);

    // Decrypt the sum
    const decryptedSum = await this.decrypt(sum);
    console.log('Decrypted Sum:', decryptedSum);
}
```

### Add a number to an encrypted one

```
<button (click)="testAddPlainToCiphertext()">Test Add Plain To Ciphertext</button>

async testAddPlainToCiphertext() {
    console.clear();
    const input = 40;
    console.log('Input:', input);
    const encrypted = await this.encrypt(input);
    console.log('Encrypted:', encrypted);

    const plain = 15;
    console.log('Plain:', plain);
    const sum = await this.sealLib.addPlainToCiphertext(encrypted, plain, true);
    console.log('Sum:', sum);

    const decryptedSum = await this.decrypt(sum);
    console.log('Decrypted Sum:', decryptedSum);
}
```

### Subtract encrypted values from each other

```
<button (click)="testSubtractCiphertexts()">Test Subtract Ciphertexts</button>

async testSubtractCiphertexts() {
    console.clear();
    const inputs = [30, 10, 5]; // Array of numbers to subtract

    // Encrypt all inputs
    const encryptedInputs = [];
    for (const input of inputs) {
        const encrypted = await this.encrypt(input);
        console.log(`Encrypted input (${input}):`, encrypted);
        encryptedInputs.push(encrypted);
    }

    // Subtract all encrypted values
    try {
        const difference = await this.sealLib.subtractCiphertexts(encryptedInputs, true);
        console.log('Subtraction Result (CipherText):', difference);

        // Decrypt the result
        const decryptedDifference = await this.decrypt(difference);
        console.log('Decrypted Difference:', decryptedDifference);
    } catch (error) {
        console.error('Error during subtraction:', error);
    }
}
```

### Subtract a number from an encrypted one

```
<button (click)="testSubtractPlainFromCiphertext()">Test Subtract Plain From Ciphertext</button>

async testSubtractPlainFromCiphertext() {
    console.clear();
    const cipherInput = 50; // Ciphertext input
    const plainInput = 20;  // Plain value to subtract

    // Encrypt the cipher input
    const encryptedInput = await this.encrypt(cipherInput);
    console.log(`Encrypted input (${cipherInput}):`, encryptedInput);

    // Subtract the plain value from the ciphertext
    try {
        const difference = await this.sealLib.subtractPlainFromCiphertext(encryptedInput, plainInput, true);
        console.log(`Subtraction Result (CipherText):`, difference);

        // Decrypt the result to verify correctness
        const decryptedDifference = await this.decrypt(difference);
        console.log(`Decrypted Difference (${cipherInput} - ${plainInput}):`, decryptedDifference);
    } catch (error) {
        console.error('Error during subtractPlainFromCiphertext:', error);
    }
}
```

### Get the current noise budget

```
<button (click)="testNoiseBudget()">Test Noise Budget</button>

async testNoiseBudget() {
    console.clear();
    const input = 1234;
    const encrypted = await this.encrypt(input);
    console.log('Encrypted:', encrypted);

    const noiseBudget = await this.sealLib.getNoiseBudget(encrypted);
    console.log('Noise Budget:', noiseBudget);
}
```
