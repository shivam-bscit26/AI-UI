# AI-UI
Generates AI UI  
import React, { useState } from 'react';
import { Wand2, Code, FileText, Sparkles, Copy, Download, Check } from 'lucide-react';

export default function AIUIGenerator() {
  const [userIdea, setUserIdea] = useState('');
  const [loading, setLoading] = useState(false);
  const [generatedUI, setGeneratedUI] = useState('');
  const [generatedDraft, setGeneratedDraft] = useState('');
  const [activeTab, setActiveTab] = useState('ui');
  const [copied, setCopied] = useState(false);

  const generateContent = async () => {
    if (!userIdea.trim()) {
      alert('Please enter your app idea first!');
      return;
    }

    setLoading(true);
    
    try {
      const response = await fetch('https://api.anthropic.com/v1/messages', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify({
          model: 'claude-sonnet-4-20250514',
          max_tokens: 4000,
          messages: [
            {
              role: 'user',
              content: `You are an expert UI/UX designer and content writer. Based on this app idea: "${userIdea}"

Please generate TWO things:

1. REACT UI CODE: Create a complete, functional React component with Tailwind CSS classes. Make it beautiful, modern, and fully functional with proper state management.

2. CONTENT DRAFT: Write professional marketing content including:
   - App description (2-3 paragraphs)
   - Key features (5-6 bullet points)
   - Target audience
   - Value proposition

Format your response EXACTLY like this:
=== UI CODE START ===
[React component code here]
=== UI CODE END ===

=== DRAFT START ===
[Content draft here]
=== DRAFT END ===`
            }
          ]
        })
      });

      const data = await response.json();
      const fullResponse = data.content
        .map(item => item.type === 'text' ? item.text : '')
        .join('\n');

      const uiMatch = fullResponse.match(/=== UI CODE START ===([\s\S]*?)=== UI CODE END ===/);
      const draftMatch = fullResponse.match(/=== DRAFT START ===([\s\S]*?)=== DRAFT END ===/);

      if (uiMatch) {
        setGeneratedUI(uiMatch[1].trim());
      } else {
        setGeneratedUI('// UI code generation failed. Please try again.');
      }

      if (draftMatch) {
        setGeneratedDraft(draftMatch[1].trim());
      } else {
        setGeneratedDraft('Draft generation failed. Please try again.');
      }

    } catch (error) {
      console.error('Error:', error);
      alert('Generation failed. Please try again.');
    } finally {
      setLoading(false);
    }
  };

  const copyToClipboard = (text) => {
    navigator.clipboard.writeText(text);
    setCopied(true);
    setTimeout(() => setCopied(false), 2000);
  };

  const downloadFile = (content, filename) => {
    const blob = new Blob([content], { type: 'text/plain' });
    const url = URL.createObjectURL(blob);
    const a = document.createElement('a');
    a.href = url;
    a.download = filename;
    a.click();
    URL.revokeObjectURL(url);
  };

  return (
    <div className="min-h-screen bg-gradient-to-br from-indigo-50 via-white to-purple-50">
      {/* Header */}
      <header className="bg-white shadow-sm border-b border-gray-200">
        <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-6">
          <div className="flex items-center gap-3">
            <div className="bg-gradient-to-br from-indigo-600 to-purple-600 p-3 rounded-xl">
              <Wand2 className="w-8 h-8 text-white" />
            </div>
            <div>
              <h1 className="text-3xl font-bold bg-gradient-to-r from-indigo-600 to-purple-600 bg-clip-text text-transparent">
                AI UI & Draft Generator
              </h1>
              <p className="text-gray-600 text-sm">Transform your ideas into reality with AI</p>
            </div>
          </div>
        </div>
      </header>

      <main className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8">
        {/* Input Section */}
        <div className="bg-white rounded-2xl shadow-lg p-8 mb-8 border border-gray-100">
          <div className="flex items-center gap-2 mb-4">
            <Sparkles className="w-6 h-6 text-indigo-600" />
            <h2 className="text-2xl font-bold text-gray-800">Describe Your App Idea</h2>
          </div>
          
          <textarea
            value={userIdea}
            onChange={(e) => setUserIdea(e.target.value)}
            placeholder="Example: A fitness tracking app that helps users log workouts, track calories, and set fitness goals. It should have a dashboard, workout history, and progress charts..."
            className="w-full h-32 p-4 border-2 border-gray-200 rounded-xl focus:border-indigo-500 focus:ring-2 focus:ring-indigo-200 transition-all resize-none text-gray-700"
          />

          <button
            onClick={generateContent}
            disabled={loading}
            className="mt-4 w-full bg-gradient-to-r from-indigo-600 to-purple-600 text-white font-semibold py-4 rounded-xl hover:shadow-xl transition-all disabled:opacity-50 disabled:cursor-not-allowed flex items-center justify-center gap-2"
          >
            {loading ? (
              <>
                <div className="w-5 h-5 border-2 border-white border-t-transparent rounded-full animate-spin" />
                Generating...
              </>
            ) : (
              <>
                <Wand2 className="w-5 h-5" />
                Generate UI & Draft
              </>
            )}
          </button>
        </div>

        {/* Results Section */}
        {(generatedUI || generatedDraft) && (
          <div className="bg-white rounded-2xl shadow-lg border border-gray-100 overflow-hidden">
            {/* Tabs */}
            <div className="flex border-b border-gray-200">
              <button
                onClick={() => setActiveTab('ui')}
                className={`flex-1 py-4 px-6 font-semibold transition-all flex items-center justify-center gap-2 ${
                  activeTab === 'ui'
                    ? 'bg-indigo-50 text-indigo-600 border-b-2 border-indigo-600'
                    : 'text-gray-600 hover:bg-gray-50'
                }`}
              >
                <Code className="w-5 h-5" />
                UI Code
              </button>
              <button
                onClick={() => setActiveTab('draft')}
                className={`flex-1 py-4 px-6 font-semibold transition-all flex items-center justify-center gap-2 ${
                  activeTab === 'draft'
                    ? 'bg-indigo-50 text-indigo-600 border-b-2 border-indigo-600'
                    : 'text-gray-600 hover:bg-gray-50'
                }`}
              >
                <FileText className="w-5 h-5" />
                Content Draft
              </button>
            </div>

            {/* Content */}
            <div className="p-6">
              {activeTab === 'ui' && (
                <div>
                  <div className="flex justify-between items-center mb-4">
                    <h3 className="text-lg font-semibold text-gray-800">Generated React Component</h3>
                    <div className="flex gap-2">
                      <button
                        onClick={() => copyToClipboard(generatedUI)}
                        className="flex items-center gap-2 px-4 py-2 bg-gray-100 hover:bg-gray-200 rounded-lg transition-all"
                      >
                        {copied ? <Check className="w-4 h-4 text-green-600" /> : <Copy className="w-4 h-4" />}
                        {copied ? 'Copied!' : 'Copy'}
                      </button>
                      <button
                        onClick={() => downloadFile(generatedUI, 'component.jsx')}
                        className="flex items-center gap-2 px-4 py-2 bg-indigo-600 text-white hover:bg-indigo-700 rounded-lg transition-all"
                      >
                        <Download className="w-4 h-4" />
                        Download
                      </button>
                    </div>
                  </div>
                  <pre className="bg-gray-900 text-gray-100 p-6 rounded-xl overflow-x-auto text-sm">
                    <code>{generatedUI}</code>
                  </pre>
                </div>
              )}

              {activeTab === 'draft' && (
                <div>
                  <div className="flex justify-between items-center mb-4">
                    <h3 className="text-lg font-semibold text-gray-800">Content & Marketing Draft</h3>
                    <div className="flex gap-2">
                      <button
                        onClick={() => copyToClipboard(generatedDraft)}
                        className="flex items-center gap-2 px-4 py-2 bg-gray-100 hover:bg-gray-200 rounded-lg transition-all"
                      >
                        {copied ? <Check className="w-4 h-4 text-green-600" /> : <Copy className="w-4 h-4" />}
                        {copied ? 'Copied!' : 'Copy'}
                      </button>
                      <button
                        onClick={() => downloadFile(generatedDraft, 'content-draft.txt')}
                        className="flex items-center gap-2 px-4 py-2 bg-indigo-600 text-white hover:bg-indigo-700 rounded-lg transition-all"
                      >
                        <Download className="w-4 h-4" />
                        Download
                      </button>
                    </div>
                  </div>
                  <div className="bg-gray-50 p-6 rounded-xl prose prose-indigo max-w-none">
                    <pre className="whitespace-pre-wrap font-sans text-gray-700">{generatedDraft}</pre>
                  </div>
                </div>
              )}
            </div>
          </div>
        )}

        {/* Features Section */}
        {!generatedUI && !generatedDraft && (
          <div className="grid md:grid-cols-3 gap-6 mt-8">
            <div className="bg-white p-6 rounded-xl shadow-md border border-gray-100">
              <div className="w-12 h-12 bg-indigo-100 rounded-lg flex items-center justify-center mb-4">
                <Code className="w-6 h-6 text-indigo-600" />
              </div>
              <h3 className="font-bold text-lg mb-2">UI Generation</h3>
              <p className="text-gray-600 text-sm">Generate beautiful, functional React components with modern design patterns</p>
            </div>

            <div className="bg-white p-6 rounded-xl shadow-md border border-gray-100">
              <div className="w-12 h-12 bg-purple-100 rounded-lg flex items-center justify-center mb-4">
                <FileText className="w-6 h-6 text-purple-600" />
              </div>
              <h3 className="font-bold text-lg mb-2">Content Drafting</h3>
              <p className="text-gray-600 text-sm">Create professional marketing content and documentation automatically</p>
            </div>

            <div className="bg-white p-6 rounded-xl shadow-md border border-gray-100">
              <div className="w-12 h-12 bg-pink-100 rounded-lg flex items-center justify-center mb-4">
                <Sparkles className="w-6 h-6 text-pink-600" />
              </div>
              <h3 className="font-bold text-lg mb-2">AI-Powered</h3>
              <p className="text-gray-600 text-sm">Leveraging Claude AI for intelligent, context-aware generation</p>
            </div>
          </div>
        )}
      </main>
    </div>
  );
}
