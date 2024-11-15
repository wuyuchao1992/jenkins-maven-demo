README: Using Maven JMeter Plugin with JMeter WebDriver Plugin

Overview

This setup is designed to run browser-based tests in JMeter using the WebDriver plugin. By using Maven, you can automate the execution of JMeter scripts, ideal for load testing scenarios that simulate user interactions in a browser.

Requirements

	1.	Java and Maven: Ensure both are installed and configured.
	2.	JMeter: Automatically downloaded through Maven, no manual installation required.
	3.	WebDriver (e.g., ChromeDriver): Download and add its path to your system’s PATH environment variable.

How to Run

	1.	Place your test scripts (.jmx files) in the src/test/jmeter folder.
	2.	Run the following command:

mvn jmeter:jmeter

This command will download JMeter dependencies and execute the test scripts.

	3.	View the test results: After completion, results will be saved in the target/jmeter/results folder.

Resources

	•	JMeter WebDriver Sampler Documentation
	•	Maven JMeter Plugin Documentation

With this simple configuration and commands, you can quickly run browser-based test scripts in JMeter and generate load testing reports.