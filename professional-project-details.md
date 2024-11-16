1. Enhancing Document Storage System for Proactive Monitoring

   - **Tech Stack**: *Java, Spring Batch, MySQL, Typescript, Angular, Material Design 3*

   - **Context**: In my role, I worked on a document storage system that managed insurance documents, claims, and customer correspondence. Documents could be uploaded through various channels, including user-facing services, physical branches, and directly by customers via email. We received documents through batch methods, such as periodic inbox scans and batched FTP transfers, as well as real-time uploads from critical services like medical insurance claims. Each service that sent documents in real time had a strict SLA of 15 minutes for resolving any issues in their processing pipelines.

   - **Shift to Proactive Monitoring**: To enhance our response to potential issues, our team decided to transition from a reactive to a proactive approach. We aimed to periodically check the operational health of the pipelines and monitor key metrics, such as:

     - Number of documents processed
     - Number of documents currently being processed
     - Files stuck in the FTP path after multiple retries
     - Compute resource consumption
    
     We had send the overview to our 24/7 support team, enabling them to take timely action to prevent outages.

   - **Designing the Solution**: I was tasked with designing the end-to-end solution, which included creating batch jobs and a dashboard for data visualization. Recognizing the massive volume of documents processed, I sought an efficient design to avoid running multiple jobs for each real-time pipeline, which would consume precious compute resources.

   - **Implementing Split Flow**: While researching Spring Batch, I discovered the **Split Flow** approach, which allows multiple independent workflows to run in parallel, each in its own thread. This was my silver bullet, enabling me to gather results from all pipelines in a single run.

   - **Optimizing Reading and Writing**: I also made the reading and writing steps multi-threaded, allowing us to process documents in chunks rather than sequentially. This optimization minimized the overall runtime of the batch job.

   - **Data Storage and API Development**: To store the analytics data collected, we added a Mysql database and developed a slim API layer to serve this data to the dashboard.

   - **Dashboard Development**: The dashboard was built using Google's Material Design 3 guidelines with Angular. For components not natively available in Angular Material, such as the date-time picker, I collaborated with third-party libraries to ensure a cohesive design.

   - **Outcome**: 
     - **Proactive Monitoring**: The new system provided real-time insights into pipeline health, allowing the support team to address issues before they escalated.
     - **Resource Efficiency**: By running parallel workflows and optimizing reading and writing, we significantly reduced compute resource usage.
     - **Improved Response Times**: The dashboard enabled proactive predictions of upcoming outages, such as when document accumulation in the FTP path exceeded processing capacity. This reduced the average response time to incidents in real-time pipelines by **58%** and decreased the number of escalations by over **70%**.
     
2. Optimizing API Efficiency for Cost Reduction
   -  **Tech Stack**: *Java, Spring Boot, Azure Cosmos DB, MongoDb, Kubernetes, Postman, Jmeter*
   - **Context**: In my role, I collaborated with a talented team on a critical internal API that aggregated data from multiple third-party service providers to assess the risk of insuring properties based on their addresses. Each provider contributed various data points, which the API consolidated to create a comprehensive property profile. However, the business incurred costs for every API call made to these providers, prompting us to explore cost-saving measures.
   - **Core Idea**: I proposed a query to fetch property details from our existing NoSQL database. If the data was retrieved within the buffer time, we reused it; otherwise, we called the third-party services. This approach aimed to reduce unnecessary API calls.
   - **Challenges**:
      - **Optimizing Query Execution Time**: Given our database contained over 3 million records and handled 
      28,000 requests per second, simple queries would time out. To address this, I collaborated with my team to implement an index on the `transactionTime` field, capturing when requests for specific addresses were made. This indexing and sorting in descending order ensured that the latest records appeared at the top, significantly improving response times.
      - **Differentiating Between Records**: To ensure accurate responses, we introduced a new field indicating the ID of the original record from which consolidated data was derived. This allowed the query to distinguish between records containing data from third-party services and those that reused existing data and ignore all the records that reused existing data.
      - **Backward Compatibility**: Throughout these changes, I worked closely with my team to ensure that backward compatibility was maintained, preventing any impact on API consumers.
   - **Further Improvements**: During load testing, I identified additional performance bottlenecks. By indexing the `address` field, I further enhanced query execution times, leading to a more responsive API.
   - **Handling Downtime**:  We observed that building the new index would render the API unavailable for over 2 hours, which was unacceptable for the business. To address this, I developed a failover query to maintain functionality during the indexing process. Although this query did not utilize the index, resulting in longer response times, it ensured users received accurate responses and the API remained operational.
   - **Canary Deployment Strategy**: For deployment, I advocated for a canary strategy instead of a rolling update to monitor the API's performance under load. We deployed the API during off-peak hours, routing 10% of production traffic to the new pods while 90% continued to use the old pods. Over 24 hours, we gradually increased traffic to 100% on the new pods, continuously monitoring API and database metrics.
   - **Outcome**: 
     - **Cost Reduction**: This initiative reduced operational costs by minimizing unnecessary API calls by 63%.
     - **Performance Improvement**: The changes brought the p90 response time down to 210ms from over 2 seconds, significantly enhancing user experience.
     - **Increased Reliability**: Overall, the API's efficiency improved, resulting in a more reliable service for our users
