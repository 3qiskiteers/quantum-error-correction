---
layout: default
title: QEC
---
THE WEBSITE IS FIXED!

This is a code block:
```c++
int main() {
  cout << "sup" << endl;
}
```

# 0. Our Goal
This project, designed by Julian Quevedo, Michael Robertson,
and Brion Ye for the Phys14N class taught by Professor Monika Schleier-Smith,
is designed to educate students about the properties and utility of quantum error correction,
the working principle behind a well-known basic error correction scheme known as Shor’s 9-bit code,
and the future of quantum error correction.

# 1. What is quantum computing? Why does it matter?
In 1994, Peter Shor created quite a stir when he introduced a quantum algorithm which could factorize large integers into their prime factors in polynomial time. For the first time, the exponential speedup and practical applications of quantum computing were realized -- at least in theory. Though Shor’s algorithm was universally acclaimed, many scientists doubted that it would ever become more than a theoretical interest.

# 2. Why do we need quantum error correction?
By the 90s, physicists were well aware of the sensitive nature of quantum systems, as even the smallest of unintended interactions could destroy the state of a qubit. They knew that quantum circuits had to be cooled to near absolute zero and be extremely well-shielded from the environment. With systems as sensitive as single atoms, it seemed inevitable that algorithms such as Shor’s would fall apart due to a phenomenon known as quantum decoherence.

Quantum decoherence is the result of an unintended interaction between a qubit and the environment, which ends up irreversibly disturbing the qubit’s wavefunction. By environment, we just mean everything outside our quantum subsystem. The environment is out of our control; we cannot measure it or apply quantum gates to investigate it. Because of this, interactions with the environment appear to destroy the quantum information in our circuit, as we are unable to interact with it to retrieve the lost information. The information is “lost” in the first place because, when two quantum objects interact, they become correlated, or entangled, “spreading” the information between them. If we could examine the universe’s wavefunction as a whole, we would see that information is conserved. However, our limited perspective only allows us to see half of the story, and hence we observe decoherence, the unpredictable, apparently non-unitary, loss of the original quantum states to the outside world. It is as if an unknown Hamiltonian has been “turned on,” or as if an unwanted, unknown measurement has been made on the system.

With no practical way to perfectly shield our quantum circuits, we had to invent another way to circumvent the sensitivity of quantum systems and create large-scale quantum computers. Only then could we finally realize the prospects of things such as Shor’s factoring algorithm. The answer was quantum error correction.

# 3. Classical vs Quantum EC
The breakthroughs in classical computing theory of the past decades have changed the world perhaps more than any other technology. We have already learned a lot about error correction in integrated circuits and transmission of data over the internet. Can’t we use that knowledge and experience and apply it to quantum circuits?

Much of classical error correction involves creating redundancies through making multiple copies of the desired information. However, measurement inherently disturbs quantum states, meaning we physically cannot create copies of an arbitrary quantum state without causing decoherence. The other difference is that classical error codes are mostly used in the transmission of data over distances. However, quantum systems are so sensitive that errors often arise even during processing, i.e. applying quantum gates. This means quantum error correcting codes will be needed much more frequently, basically all the time.

# 4. Types of Quantum Errors
Bit-flip codes account for situations where |0> gets flipped to |1> or |1> gets flipped to |0>. These errors are represented by the X operator.

Sign-flip codes, or phase-flip codes, account for situations when |0> + |1> becomes |0> - |1>. These kinds of errors are represented by the Z operator.

Both of these types of phenomenon are encountered often in nature. In order to have what is known as a complete error correcting code, it must be able to deal with both bit-flips and phase-flips.

# 5. The Bit-Flip Code
The first step of the process is encoding. Here we’re going to take advantage of quantum mechanics and create an entangled state. Psi is the state we want to keep in tact at the end of the day. Then, we have two CNOT gates applied one after another. This entangles all three qubits.

Next is the random environmental error. Since we are doing the bit flip code, we can correct for any rotation around the x axis.

In the decoding stage, this is where we hopefully ensure psi has the correct state. The CNOTs are applied once more, followed by a Toffoli. The Toffoli will only activate if  both ancillas are a 1 at the time.

Let’s say each qubit in our circuit has probability p of X being applied to it “by the environment” every timestep. To account for this, we can define one logical qubit using 3 physical qubits. For example, the |0> logical state will be expressed as |000> and the |1> logical state will be expressed as |111>. A classical algorithm would achieve this by simply making two copies of the original bit, then taking a “majority vote” at the end of processing. However, creating a copy of our desired qubit would necessarily disturb it. So, we instead distribute its information across two ancilla qubits using two sequential CNOT gates. The ancilla qubits are initially in the |0> state, so, If our arbitrary state psi is in the |1> state, they will get flipped to the |1> state as well. If psi is in the |0> state, nothing happens, and all qubits remain in the |0> state.

Error codes are generally broken into a 3 step process: encoding, simulated error, and decoding. What we have so far is the encoding stage. Next is the error simulation, also called a “noisy channel,” in which any subset of three qubits may have X applied to them at random. Since p is the probability of a bit-flip, Each qubit has a probability of 1-p of being transmitted correctly.

The rest of the circuit involves applying another two CNOT gates in a similar fashion, followed by a CCNOT or Toffoli gate on psi. The Toffoli gate will only flip psi if both ancilla bits are in the |1> state. So, assuming only 1 qubit has been flipped in the noisy channel, let’s see how this circuit works!

If the first bit is flipped, then the ancilla bits always become the |1> state, and thus the Toffoli takes care of the correction. If either of the ancilla bits are flipped, the Toffoli will not activate, so the state of psi is not disturbed.

What happens if two bits are flipped? In this case, two bit-flips are indistinguishable from a single bit-flip on the other qubit. For example, if psi and one of the ancilla bits are flipped, the Toffoli will never activate, as the two ancilla bits will always be different. This means that psi will not be reverted to its original value. If instead it is both ancilla bits which are flipped, the Toffoli will activate on a false-positive, as they will both end up in the |1> state. This time, psi is accidentally flipped into the incorrect state, even though it was correct in the first place.

What if all three bits are flipped? This situation turns out to be equivalent to no flips occurring at all. In this case, both ancilla bits will always end up in the |0> state, which will fail to activate the Toffoli and leave psi in the incorrect state.

In conclusion, the 3-bit code can only account for a single bit flip. In general, quantum error codes are unable to correct for all possible errors. So, they are optimized for the most probable errors.

Concretely, how much does using the 3-bit code help us? We know it will only work if 0 or 1 of our 3 qubits are flipped. The probability of none being flipped is (1-p)^3, while the probability of any single qubit being flipped is p(1-p)^2. So, the combined probability of our error code reproducing the correct state is (1-p)^3 + 3p(1-p)^2. If we didn’t use error correction, the probability of psi being correctly transmitted is just 1-p, as mentioned before. The probability of perfectly reproducing the original state is a measure of what’s called fidelity. Plotting the fidelity vs p, we see that the 3-bit code will result in higher fidelity as long as the probability of a bit flip is less than 50%. Once the probability becomes higher than 50%, however, the problems associated with 2 or 3 bit-flips dominate.

# 6. The Phase-Flip Code
As it is a close cousin of the bit-flip code, the circuit for correcting random phase-flips is very similar. The only difference is that there are three extra Hadamard gates at the end of the encoding sequence, and three more at the beginning of the decoding sequence.

For example, let’s say psi is |0>. It will become 1/sqrt(2)(|0> + |1>) after passing through the Hadamard gate. Then, due to some environmental disturbance, it flips to the 1/sqrt(2)(|0> - |1>) state. After passing through the second Hadamard, it is now in the |1> state. The rest of the circuit decodes in exactly the same way as the bit-flip code did.

# 7. The Complete Shor 9 Bit Code
Include a formulation of “unitary error” (as a sum of I, X, and Z gates)
Justify the completeness of Shor’s code by plotting the gates’ effects on the Bloch sphere.
Circuit and code examples
Real-world example by showing the code running on a real quantum computer
Maybe a graph on whether this actually increases or decreases error
Demonstrate the recursive nature of the circuit
# 8. The future of quantum error correction

Although Shor’s 9-bit algorithm was a breakthrough in the sense that it provided the first example of working error correction code for quantum computing, it has quite a few limitations. As a result, it’s not in the running as actual error correction, but was crucial as a proof of concept and helped to propel the field forwards.

One main limitation is that it can only handle one arbitrary error. While there are certain arrangements of errors where more than one can be handled, such as a bit flip in each block (totalling three errors), in general one is the limit. For example, if bit flips occur on two qubits in the same block, our bit flip correction code fails to work, giving us the wrong state in the end.

Another limitation is that we assume the error occurs only in one region, and that all of our quantum gates are infallible. In reality though, there’s no reason why noise from the environment couldn’t cause errors in our qubits in other parts of the code. Also, it’s possible for quantum gates to introduce errors as well, since hardware isn’t perfect. Shor’s code breaks down once you drop the nice theoretical assumptions we made.

Another limitation is that what we’re preserving is a single qubit. This doesn’t make for a very good logical qubit -- something we can perform quantum algorithms with without worrying much about errors. Even if Shor’s code protects our qubit from an error, immediately after the code, an error could occur on the qubit. Now, if we tried running Shor’s code, the error wouldn’t be fixed, because the new state caused by the error is what would be distributed among the 9 qubits. Modern error correction codes involve logical qubits composed of many physical qubits. That way, when single qubit errors happen, they don’t destroy the whole logical qubit, and can be detected and corrected for.

Before briefly discussing some of the other correction codes out there, we’ll look at something called the quantum threshold theorem.

Quantum threshold theorem
Major error correction alternatives
Especially surface code
Takeaway

References
Quantum Error Correction for Beginners
https://arxiv.org/pdf/0905.2794.pdf

Scheme for reducing decoherence in quantum computer memory
https://www.cs.miami.edu/home/burt/learning/Csc670.052/pR2493_1.pdf

Quantum Error Correction
https://oxfordre.com/physics/view/10.1093/acrefore/9780190871994.001.0001/acrefore-9780190871994-e-35

25 Years of quantum error correction
https://www.nature.com/articles/s42254-020-0244-y.pdf?origin=ppub

Quantum error correction on Wikipedia
https://en.wikipedia.org/wiki/Quantum_error_correction

Motives behind quantum error correction
http://decodoku.blogspot.com/2016/02/4-decoding-without-looking.html?m=1
