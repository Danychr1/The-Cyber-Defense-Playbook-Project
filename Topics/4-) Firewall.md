## Firewall Log Analysis Techniques

### Challenge: 
Analyzing logs from diverse, potentially outdated, or "janky" technologies (e.g., firewalls, web servers) where log formats and security settings vary significantly.

### Goal: 
Develop skills to analyze any log file, rather than relying on a SIM tool to parse logs perfectly or only knowing how to analyze specific types.

### Tools and Techniques:
  - RBS core libraries: Installed on a Kali system, these allow for mathematical and statistical analysis of log data.
  - grep: Used to search for specific strings within logs, enabling inclusion of matching lines or exclusion of unwanted lines (e.g., grep -v).
  - cut: Used to extract specific offsets and fields (columns) from log data, focusing on relevant information.
    
### Process:
  1.) Clean and refine log data by filtering for interesting patterns and excluding irrelevant ones.
  
  2.) Focus on specific column fields.
  
  3.) Perform mathematical analysis on refined data (e.g., calculate minimum, maximum, mean, standard deviation, and variance). 
