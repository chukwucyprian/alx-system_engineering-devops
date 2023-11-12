###0x19. Postmortem
#The Great "Phantom 'P'" Chase - ALX's System Engineering Odyssey Unveiled! 🚀

#Incident Overview:
#Duration:
Commencing on the morning of the release of ALX's System Engineering & DevOps project 0x19 at approximately 06:00 West African Time (WAT) in Nigeria, the incident persisted until resolution around 19:20 PST, spanning several hours of unexpected downtime.

#Symptoms:
The Apache web server within an isolated Ubuntu 14.04 container went rogue, responding to innocent GET requests with a rebellious 500 Internal Server Error instead of serving up the expected HTML file for a Holberton WordPress site.

#Debugging Odyssey:
Our intrepid bug debugger, Bamidele (aka Lexxyla, master of impromptu aliases), embarked on a quest around 19:20 PST to unveil the mysteries behind the unruly Apache server.

*Process Check:

Examined running processes using ps aux.
Two apache2 processes (root and www-data) were seemingly well-behaved.

*Apache Configuration Exploration:

Delved into the /etc/apache2/sites-available folder, discovering the server served content from /var/www/html/.
*Strace Adventure:

Ran strace on the root Apache process PID while simultaneously curling the server.
Disappointment ensued as strace provided no useful insights.
*Success Amidst Despair:

Repeated strace on the www-data process PID.
Jackpot! Unveiled an -1 ENOENT error, signaling the absence of /var/www/html/wp-includes/class-wp-locale.phpp.
*File Hunt:

Searched files in /var/www/html/ for the phantom .phpp extension.
Unearthed the culprit in wp-settings.php at line 137: require_once( ABSPATH . WPINC . '/class-wp-locale.phpp' );.
*Fixing the Typo:

Trimmed the erroneous trailing 'p' from the line.
Celebration ensued as another curl test returned a splendid 200 OK response.
*Automating Redemption:

Wrote a Puppet manifest (0-strace_is_your_friend.pp) to automate fixing similar errors.
The manifest surgically replaces any 'phpp' extensions in /var/www/html/wp-settings.php with 'php'.
#Root Cause and Resolution:
A classic typo! The WordPress app stumbled upon a critical error in wp-settings.php when attempting to load the non-existent file class-wp-locale.phpp. The correct file, located in the wp-content directory, was class-wp-locale.php. The solution? A simple snip, removing the trailing 'p'.

#Preventive Measures:

*Thorough Testing:

Emphasize rigorous testing before deploying any application changes to catch errors like typos early in the development lifecycle.
*Status Monitoring:

Implement uptime monitoring services such as UptimeRobot to instantly alert in case of website outages.
*Automation Saves the Day:

Leveraged automation with the Puppet manifest (0-strace_is_your_friend.pp) to swiftly address identical errors automatically.

#Conclusion:
In the grand drama of system engineering and DevOps, even the mightiest can fall due to a humble typo. Lessons learned, automation embraced, and the confidence that, as programmers, we are impervious to future errors (wink, wink). The web server may have stumbled, but ALX's project emerged victorious in the end, ready for the next challenge that the digital frontier may throw our way!
