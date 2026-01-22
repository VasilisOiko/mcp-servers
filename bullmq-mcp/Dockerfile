# BullMQ MCP Server Dockerfile
FROM node:20-slim

# Set working directory
WORKDIR /app

# Install the BullMQ MCP server package using npx
# Based on: https://lobechat.com/discover/mcp/adamhancock-bullmq-mcp
# The package name may need to be verified from the LobeChat deployment page
# Common patterns: @adamhancock/bullmq-mcp, bullmq-mcp, or @bullmq/mcp

# MCP servers communicate via stdio, no port needed
# But keeping this for potential HTTP-based usage
EXPOSE 3000

# Start the MCP server using npx
# Update the package name if different from the actual npm package
# Alternative: If the package requires installation first, use:
# RUN npm install -g @adamhancock/bullmq-mcp
# CMD ["bullmq-mcp"]
CMD ["npx", "-y", "@adamhancock/bullmq-mcp"]
