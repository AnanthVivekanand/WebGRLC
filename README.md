
# WebGRLC

Is a Javascript cryptocurrency miner that *runs in a users web browser* and mines Allium-based coins, namely GRLC.
 
Concept: Users that visit a website can utilize their spare CPU cycles to support the website. I believe browser-based cryptocurrency mining has a wide potential area to be used in - from such as in-game rewards to as an alternative to marketing ads. I believe browser-based cryptocurrency mining when used properly and with caution, can help support websites while offering a better browsing experience for the end user.


## Getting Started

These instructions will get you a copy of the project up and running on your local machine for development and testing purposes. See deployment for notes on how to deploy the project on a live system.

### Prerequisites

WebGRLC depends on a couple things. I will assume you have a debian-based system to install dependencies on.


##### 1. Apache Web Server

```
sudo apt update
sudo apt install apache2
sudo ufw allow in "Apache Full"
```
The last command opens up the firewall to allow traffic to ports 80 and 443.

##### 2. a PHP installation

```
sudo apt install php libapache2-mod-php php-mysql
sudo systemctl restart apache2
```
The last command restarts Apache Web Server, and will allow it to use php.
### Installing

Before we install the WebGRLC Miner, it would be helpful for us to understand how the web miner works. Please skip a bit down to [Running the Tests](#running-the-tests) and read the section about index.html, which explains how the web miner operates. Now that you've done so and we understand the web miner, let's install it to our system!

First, let's download all our files into our web server. Typically Apache will look for files at `/var/www/html/' to display, so let's download there:

```
cd /var/www/html
sudo git clone https://github.com/KorkyMonster/alliumhash-wasm.git
```

Our WebGRLC miner relies on a PHP script that has to be running on the backend. First let's modify run.php - we need to modify our pool info and the location of ``ww.txt``. The tricky bit is that we have to make sure that ww.txt can be written to from the php script. Let's create ww.txt and j.txt, and assign all neccesary permissions.

```
sudo mkdir ~/WebGRLC
sudo chmod a+rwx ~/WebGRLC
touch ~/WebGRLC/ww.txt
touch ~/WebGRLC/j.txt
```

Then we'll edit `run.php` and fill in our pool info and the location of `ww.txt`. The now have to edit `StratumServer.php` and fill in the location of `j.txt`. We finally need to edit `w.php` to fill in the locations of both `ww.txt` and `j.txt`. The values that I used when installing were `/home/raptor/utils/ww.txt` and `/home/raptor/utils/j.txt`

## Running the tests

### `index.html`
I made a simple UI for WebGRLC in `index.html`. Loading `index.html` in a web browser should allow you to test whether your installation is working. But first, let's understand how it works:

* When `index.html` is loaded, it loads with it an additional script called `work_manager.js`
* `work_manager.js` doesn't start mining immediately, it waits for a user to click the start mining button on `index.html`. When the start mining button is clicked on `index.html`, a function is called in `work_manager.js` that initiates the process to start mining.
* `work_manager.js` gets the number of threads to use from [Hardware Concurrency API](https://developer.mozilla.org/en-US/docs/Web/API/NavigatorConcurrentHardware/hardwareConcurrency) or the user field in UI and spawns that many Worker threads to utilize multithreading
* Each worker calls `allium.js`, which is a script that contains hashing functions, which then loads `allium.wasm` which has low-level code to carry out hashing.
* Each worker initializes variables needed for mining and awaits work to process from `work_manager.js`.
* Meanwhile on the backend, we have a 
script called `run.php` being run. `run.php` communicates with the mining pool and network and receives any work assigned to it by the pool. It then writes the work to a file named `ww.txt` and `j.txt`, in a location of your configuration.
* `work_manager.js` AJAX's w.php to get the latest work from `run.php` and distributes to worker threads, which then start mining, and feeding back found shares and hashrate info at intervals.
* `work_manager.js` continues overseeing worker threads by updating work given by the mining pool and taking action on user UI.

## Deployment

I strongly belive browser-based cryptocurrency mining can reap rewards for both the website and end user, but only if it is managed properly. It is highly advised you leave a minimum of a thread on a user's computer unused. Using too much of a computer's resources can put strain on the system and make the webpage unresponsive. It is also cautioned that this is automatically disabled for mobile devices that rely on batteries. Mining on mobile devices will quickly drain their batteries and could cause damage to the devices.

## Built With

* [PHP](http://www.php.net/) - PHP is a popular general-purpose scripting language that is especially suited to web development.
* [WASM](https://webassembly.org/) - WebAssembly (abbreviated Wasm) is a binary instruction format for a stack-based virtual machine. 
* A lot of love!  ❤️ ❤️ ❤️

## Contributing

Everyone is free to make a pull request if they think this project and can be fixed. I will always review all PRs before merging. If I think it's good, I will!

## Authors

* **KorkyMonster#4304** - *Implemented Allium hashing algorithms, optimized miner, implemented hashrate reporting*
* **[Ryan Shaw](https://github.com/ryan-shaw)** - *Provided low-level (WASM) code*
* **[borlak](https://github.com/borlak/)** - *Provided php framework and basic front-end structure*

## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details

## Acknowledgments

* Ryan Shaw - ❤️ ❤️ ❤️. Mr. Shaw is a Garlicoin developer, who helped me so much through this project. He was very knowledgable about everything and was able to graciously provide the low-level (WASM) code that was used to make this miner.
* [The Blockchain Developers Club](https://discord.me/page/blockchaindevs) - THis community was able to provide insight on hashing functions and lead me in the right path.
* [cryptocoin_scrypt_stratum](https://github.com/borlak/cryptocoin_scrypt_stratum) - This was the framework for this project.

