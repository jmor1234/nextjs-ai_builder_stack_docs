# Core Use Cases and Selection Guide

## Next.js
- **Primary Role**: Foundational framework for both frontend and backend development.
- **Usage Scope**: Integral to nearly all aspects of the application.
- **Key Point**: Almost every task or question will involve Next.js to some degree.

## Convex
- **Primary Role**: Backend operations, database management, vector storage and retrieval, and file storage.
- **Authentication**: Integrates with Clerk for all authentication needs (client-side and server-side).
- **Key Point**: For any authentication-related tasks, refer to the Clerk Authentication section in the Convex documentation.

## ShadCN/UI
- **Primary Role**: Frontend UI/UX component library.
- **Usage Approach**: 
  1. First option for all UI components.
  2. Non-ShadCN components may be used if they significantly enhance overall UI/UX.
- **Key Point**: Prioritize ShadCN components, but remain flexible for optimal user experience.

## Vercel AI SDK
- **Primary Role**: Core toolkit for all AI-related functionalities.
- **Scope**: Covers AI components across frontend, backend, and server actions.
- **Key Point**: Default choice for any AI-related tasks or questions.

## Decision-Making Framework
1. Assess the primary nature of the task (frontend, backend, database, AI, authentication).
2. Consider Next.js as the foundational layer for implementation.
3. For specific functionalities:
   - Backend/Database/Vector → Convex
   - Authentication → Convex (Clerk integration)
   - UI components → ShadCN/UI (with flexibility)
   - AI functionalities → Vercel AI SDK
4. Evaluate potential integrations between libraries for complex tasks.