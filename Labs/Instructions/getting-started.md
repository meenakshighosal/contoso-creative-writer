# Build Creative App using Azure AI and Prompty

### Overall Estimated Duration : 1 Hour

## Overview

Contoso Creative Writer is an intelligent app designed to streamline the creation of well-researched, product-specific articles. Users can simply input the required information and click "Start Work" to initiate the process. For those interested in observing the inner workings, a debug button in the bottom right corner of the screen allows a step-by-step view of the agent workflow. As the agents complete their respective tasks, the article draft begins to appear on the screen.

This lab showcases how to develop and utilize AI-driven agents with Azure OpenAI. Built with FastAPI, the application orchestrates a series of specialized agents: a research agent uses the Bing Search API to gather topic-specific insights, a product agent employs Azure AI Search to find relevant products via semantic similarity in a vector store, a writer agent combines research and product information into a structured article, and an editor agent refines the final draft before presentation. Together, these agents deliver a seamless, automated writing experience, transforming user instructions into high-quality, informative articles.

## Objective

Learn how to create and orchestrate AI-driven agents for automated content generation using Azure OpenAI and FastAPI. Gain hands-on experience in integrating multiple AI services, conducting research with APIs, and refining content to produce high-quality, product-specific articles. By the end of this lab, you will be able to:

- **Cloud Environment Setup:** Configure the necessary Azure resources to support and manage the AI-driven content generation workflow.

- **Agent Orchestration:** Implement and coordinate multiple specialized agents, including research, product, writer, and editor agents, each with a distinct role in the content creation process.

- **API and AI Service Integration:** Use the Bing Search API for topic research and Azure AI Search for product data retrieval, enhancing the relevance and depth of generated content.

- **Content Refinement and Presentation:** Assemble research and product information into a cohesive article, refine it with the editor agent, and deliver polished content for end users.

## Pre-requisites

- **Familiarity with Azure:** Basic knowledge of Azure services and the Azure portal for managing cloud resources.

- **Basic Knowledge of Python:** Familiarity with Python programming to work with the FastAPI framework and understand the agent workflows.

- **Understanding of Azure Developer CLI:** Basic understanding of how to deploy resources using Azure Developer CLI.

## Architecture

The architecture includes a series of AI-driven agents working together to produce high-quality, product-specific articles. User input flows through Azure Container Apps (ACA) with the security of Azure Managed Identity. The Researcher Agent uses Azure OpenAI, Bing Search, and Azure AI Search to gather relevant information, while the Writer Agent structures this content into an article, and the Editor Agent refines it for clarity and quality. Application Insights monitors the system's performance, ensuring a seamless experience for users as they receive their final, polished content.

## Architecture Diagram

![](../media/Creative_writing_aca.png)

## Explanation of Components

1. **Azure Container Apps (ACA):** A managed environment (powered by Azure Container Apps) for deploying and scaling containerized applications. It orchestrates the AI agents and manages application lifecycle and scaling without requiring direct infrastructure oversight.

2. **Researcher Agent:** This agent leverages **Azure OpenAI** for natural language processing, **Bing Search API** for real-time web searches, and **Azure AI Search** for performing semantic searches in a vector store. It gathers relevant topic and product data to inform article creation.

3. **Writer Agent:** Utilizes **Azure OpenAI** to take the gathered research and product information and draft a cohesive, structured article. This agent focuses on creating an informative and readable piece of content.

4. **Editor Agent:** Uses Azure OpenAI to enhance and refine the draft article, improving clarity, grammar, and flow, ensuring that the final output is polished and reader-friendly.

5. **Application Insights:** Part of Azure Monitor, this service tracks the performance and health of the application, logging metrics and providing alerts. It enables monitoring for optimization and helps ensure reliability of the overall system.

## Getting Started with Lab

Welcome to your Build Creative App using Azure AI and Prompty Lab! We've prepared a seamless environment for you to explore and learn. Let's begin by making the most of this experience.

### Accessing Your Lab Environment

Once you're ready to dive in, your virtual machine and lab guide will be right at your fingertips within your web browser.

[img1]

### Exploring Your Lab Resources

To get a better understanding of your lab resources and credentials, navigate to the Environment tab.

[img]

### Utilizing the Split Window Feature

For convenience, you can open the lab guide in a separate window by selecting the Split Window button from the Top right corner

[img]

### Managing Your Virtual Machine

Feel free to start, stop, or restart your virtual machine as needed from the Resources tab. Your experience is in your hands!

[img]

## Let's Get Started with Azure Portal

1. In the JumpVM, click on Azure portal shortcut of Microsoft Edge browser which is created on desktop.

2. On **Sign into Microsoft Azure** tab you will see login screen, in that enter following email/username and then click on **Next**.

   - Email/Username: <inject key="AzureAdUserEmail"></inject>

3. Now enter the following password and click on **Sign in**.

   - Password: <inject key="AzureAdUserPassword"></inject>

   >**Note:** If you see the Action Required dialog box, then select Ask Later option.

4. If you see the pop-up **Stay Signed in?**, click No.

5. If you see the pop-up **You have free Azure Advisor recommendations!**, close the window to continue the lab.

6. If a **Welcome to Microsoft Azure** popup window appears, click **Cancel** to skip the tour.

7. Now, click on the **Next** from lower right corner to move on next page.

## Support Contact

1. The CloudLabs support team is available 24/7, 365 days a year, via email and live chat to ensure seamless assistance at any time. We offer dedicated support channels tailored specifically for both learners and instructors, ensuring that all your needs are promptly and efficiently addressed.Learner Support Contacts:

   - Email Support: labs-support@spektrasystems.com
   - Live Chat Support: https://cloudlabs.ai/labs-support

2. Now, click on Next from the lower right corner to move on to the next page.

## Happy Learning!!


