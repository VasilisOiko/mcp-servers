# Ant Design MCP Server Dockerfile
FROM node:20-slim

# Install git and other dependencies
RUN apt-get update && apt-get install -y \
    git \
    && rm -rf /var/lib/apt/lists/*

# Set working directory
WORKDIR /app

# Clone the Ant Design MCP server repository
RUN git clone https://github.com/hannesj/mcp-antd-components.git . && \
    npm install

# Note: Ant Design MCP requires documentation extraction
# This step requires cloning the Ant Design repo first
# Uncomment and adjust the path if you want to extract docs during build:
# RUN git clone https://github.com/ant-design/ant-design.git /tmp/ant-design && \
#     npm run extract /tmp/ant-design || echo "Extraction skipped - run manually if needed"

# MCP servers communicate via stdio, no port needed
# But keeping this for potential HTTP-based usage
EXPOSE 3000

# Start the MCP server
CMD ["node", "index.mjs"]
