# Create a clone of ‘cat /dev/random’

I was tasked with creating a clone of running the Linux command: _cat /dev/random_

My initial research led me to a few interesting places on the interwebs:

# LinuxFromScratch
## http://www.linuxfromscratch.org/hints/downloads/files/entropy.txt

I really enjoyed the explanation of entropy and a true randomness by Robert Connolly:

"Generating true entropy in a computer is fairly difficult because nothing, outside of quantum physics, is random. The Linux kernel uses keyboard, mouse, network, and disc activities, with a cryptographic algorithm (SHA1), to generate data for the /dev/random device. One of the problems with this is that the input is not constant, so the kernel entropy pool can easily become empty. The /dev/random device is called a "blocking device". This means if the entropy pool is empty applications trying to use /dev/random will have to wait, indefinitely, until something refills the pool. This is both a feature and a nuisance, and can cause a denial of service depending on the application. Another problem with using the keyboard, mouse, network, and disc activity is that on idle, unmanned, and disc-less systems there is very little, or no, input of this kind. It is also theoretically possible for an observer (keyboard or network sniffer) to predict the entropy pool without having root level access. The only real solution to these vulnerabilities is in using a hardware-based random number generator. These hardware devices usually use electrical static as a source of entropy, because there is currently no technology that can reliably predict this. The best hardware random number generators use radioactive decay as an entropy source."


# Haveged
## https://www.digitalocean.com/community/tutorials/how-to-setup-additional-entropy-for-cloud-servers-using-haveged
- Based on the HAVEGE principle, and previously based on its associated library, haveged allows generating randomness based on variations in code execution time on a processor.

# My Pseudo-Solution(s)
- I have to admit that I don't have the coding experience to deliver a true clone of _cat /dev/random_ .
- However, I did find a solid source of entropy; the ANU Quantum Random Numbers Server -> **http://qrng.anu.edu.au**. "This website offers true random numbers to anyone on the internet. The random numbers are generated in real-time in our lab by measuring the quantum fluctuations of a vacuum."
- The ideal solution would be to query their API as a entropy pool and dump the data to STDOUT.
- After doing some more digging, I found a Github project that does just that -> **https://github.com/kuberkaul/randomclone**
- I also found a Python tool that interfaces with the same entropy pool and provides the `qrandom` CLI -> **https://pypi.org/project/quantumrandom/**

So running the following commands in Terminal would replicate true random entropy being dumped to the screen:
```python
sudo pip install quantumrandom
qrandom --binary
```
Another naive solution is to use Python's os.urandom() module.
```python
import os
print(os.urandom(100000000))
```
Saving this to rand.py and executing `python rand.py` would dump a large amount of random data (and unfortunately probably hang your Terminal session)
