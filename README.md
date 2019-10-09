I was tasked with creating a clone of running the Linux command: `cat /dev/random`

My initial research led me to a few interesting places on the interwebs:

## [LinuxFromScratch](http://www.linuxfromscratch.org/hints/downloads/files/entropy.txt)

I really enjoyed the explanation of entropy and a true randomness by Robert Connolly:

_"Generating true entropy in a computer is fairly difficult because nothing, outside of quantum physics, is random. The Linux kernel uses keyboard, mouse, network, and disc activities, with a cryptographic algorithm (SHA1), to generate data for the /dev/random device. One of the problems with this is that the input is not constant, so the kernel entropy pool can easily become empty. The /dev/random device is called a "blocking device". This means if the entropy pool is empty applications trying to use /dev/random will have to wait, indefinitely, until something refills the pool. This is both a feature and a nuisance, and can cause a denial of service depending on the application. Another problem with using the keyboard, mouse, network, and disc activity is that on idle, unmanned, and disc-less systems there is very little, or no, input of this kind. It is also theoretically possible for an observer (keyboard or network sniffer) to predict the entropy pool without having root level access. The only real solution to these vulnerabilities is in using a hardware-based random number generator. These hardware devices usually use electrical static as a source of entropy, because there is currently no technology that can reliably predict this. The best hardware random number generators use radioactive decay as an entropy source."_

## [Haveged](https://linux.die.net/man/8/haveged)
- I learned that a common problem for headless machines (that can't rely on the mouse/keyboard to generate entropy) is that the entropy runs out. And since `/dev/random` is a blocking device, it will wait indefinitely until the entropy pool is refilled.
- The HAVEGE principle allows generating randomness based on variations in code execution time on a processor.
- In essence, Haveged is a great solution for boosting entropy and improving cryptographic randomness.

## Quantum Random Numbers
- I did find a solid source of entropy; the [ANU Quantum Random Numbers Server](http://qrng.anu.edu.au).
- _"This website offers true random numbers to anyone on the internet. The random numbers are generated in real-time in our lab by measuring the quantum fluctuations of a vacuum."_
- The ideal alternative solution to `cat /dev/random` would be to query that quantum entropy pool via their API and dump the data to STDOUT.
- After doing some more digging, I found a [Github project](https://github.com/kuberkaul/randomclone) that does just that. 
- I also found a [Python tool](https://pypi.org/project/quantumrandom/) that interfaces with the same quantum entropy pool and provides the `qrandom` CLI.

### CSPRNGs
I also learned the difference between **(PRNG)** _Pseudo Random Number Generators_ and **(CSPRNG)** _Cryptographically Secure Pseudo Random Number Generators_. PRNGs are not truly random because they generate a pseudo-random sequence based on the initial seed. Thus, they should be used for statistical modeling, simulation, and to make random data reproducible. CSPRNGs, on the other hand, read random bytes from `/dev/random` and thus work best for security and cryptographic applications where data sensitivity is imperative.

## My Pseudo-Solutions
- I have to admit that currently I do not have the engineering experience to deliver a true clone of `cat /dev/random`.
- However, I did utilize the `quantumrandom` Python tool + Python's `os.urandom()` module (which doesn't rely on manual seeding) to create two naive solutions:

### Quantum.sh
```
git clone https://github.com/yevgen93/random.git
cd random
chmod +x quantum.sh
./quantum.sh
```
### Rand.py (assuming Python is installed)
```
git clone https://github.com/yevgen93/random.git
cd random
python rand.py
```
