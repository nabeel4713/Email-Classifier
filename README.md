# Email-Classifier
Email Classifier AI
import { useState } from 'react'
import { Card, CardContent, CardHeader, CardTitle } from "/components/ui/card"
import { Button } from "/components/ui/button"
import { Mail, Briefcase, User, Shield, Tag } from "lucide-react"

type EmailCategory = 'work' | 'personal' | 'spam' | 'promotions'

interface Email {
  id: string
  sender: string
  subject: string
  preview: string
  date: string
  category: EmailCategory
  read: boolean
}

const categoryIcons = {
  work: <Briefcase className="h-4 w-4" />,
  personal: <User className="h-4 w-4" />,
  spam: <Shield className="h-4 w-4" />,
  promotions: <Tag className="h-4 w-4" />
}

const categoryColors = {
  work: 'bg-blue-100 text-blue-800',
  personal: 'bg-green-100 text-green-800',
  spam: 'bg-red-100 text-red-800',
  promotions: 'bg-purple-100 text-purple-800'
}

// Simulated AI classifier function
const classifyEmail = (subject: string, sender: string): EmailCategory => {
  const subjectLower = subject.toLowerCase()
  const senderLower = sender.toLowerCase()

  if (senderLower.includes('@company.com') || subjectLower.includes('meeting') || subjectLower.includes('report')) {
    return 'work'
  } else if (subjectLower.includes('sale') || subjectLower.includes('discount') || subjectLower.includes('offer')) {
    return 'promotions'
  } else if (subjectLower.includes('urgent') || subjectLower.includes('winner') || subjectLower.includes('free')) {
    return 'spam'
  }
  return 'personal'
}

// Generate mock emails with simulated classification
const generateMockEmails = (): Email[] => {
  const mockEmails = [
    {
      id: '1',
      sender: 'hr@company.com',
      subject: 'Monthly Team Meeting',
      preview: 'Please join us for the monthly team meeting this Friday...',
      date: '10:30 AM',
      read: false
    },
    {
      id: '2',
      sender: 'amazon@shopping.com',
      subject: 'Your order has shipped!',
      preview: 'Your recent order #12345 has been shipped and will arrive...',
      date: 'Yesterday',
      read: true
    },
    {
      id: '3',
      sender: 'john.doe@gmail.com',
      subject: 'Weekend plans',
      preview: 'Hey! Are we still on for hiking this weekend?',
      date: 'Yesterday',
      read: false
    },
    {
      id: '4',
      sender: 'notifications@social.com',
      subject: 'You won a free iPhone!',
      preview: 'Congratulations! You have been selected to receive a free...',
      date: 'Jun 20',
      read: true
    },
    {
      id: '5',
      sender: 'newsletter@tech.com',
      subject: 'This Week in Tech: Latest Updates',
      preview: 'Here are the biggest tech stories of the week...',
      date: 'Jun 19',
      read: false
    },
    {
      id: '6',
      sender: 'support@service.com',
      subject: 'Your subscription is expiring soon',
      preview: 'Your premium subscription will expire in 7 days...',
      date: 'Jun 18',
      read: true
    }
  ]

  return mockEmails.map(email => ({
    ...email,
    category: classifyEmail(email.subject, email.sender)
  }))
}

export default function EmailClassifier() {
  const [emails] = useState<Email[]>(generateMockEmails())
  const [selectedCategory, setSelectedCategory] = useState<EmailCategory | 'all'>('all')
  const [selectedEmail, setSelectedEmail] = useState<Email | null>(null)

  const filteredEmails = selectedCategory === 'all' 
    ? emails 
    : emails.filter(email => email.category === selectedCategory)

  return (
    <div className="min-h-screen bg-gray-50 p-4 md:p-8">
      <Card className="max-w-6xl mx-auto">
        <CardHeader>
          <CardTitle className="flex items-center gap-2">
            <Mail className="h-6 w-6" />
            <span>Email Classifier</span>
          </CardTitle>
        </CardHeader>
        <CardContent>
          <div className="flex flex-col md:flex-row gap-6">
            {/* Sidebar */}
            <div className="w-full md:w-64 space-y-4">
              <div className="space-y-1">
                <Button 
                  variant={selectedCategory === 'all' ? 'default' : 'ghost'} 
                  className="w-full justify-start"
                  onClick={() => setSelectedCategory('all')}
                >
                  All Emails
                </Button>
                {Object.entries(categoryIcons).map(([category, icon]) => (
                  <Button
                    key={category}
                    variant={selectedCategory === category ? 'default' : 'ghost'}
                    className="w-full justify-start"
                    onClick={() => setSelectedCategory(category as EmailCategory)}
                  >
                    <span className="mr-2">{icon}</span>
                    {category.charAt(0).toUpperCase() + category.slice(1)}
                  </Button>
                ))}
              </div>
            </div>

            {/* Email List */}
            <div className="flex-1">
              {selectedEmail ? (
                <div className="space-y-4">
                  <Button variant="outline" onClick={() => setSelectedEmail(null)}>
                    Back to list
                  </Button>
                  <Card>
                    <CardHeader>
                      <div className="flex justify-between items-center">
                        <h3 className="text-xl font-semibold">{selectedEmail.subject}</h3>
                        <span className={`px-2 py-1 rounded-full text-xs font-medium ${categoryColors[selectedEmail.category]}`}>
                          {selectedEmail.category.charAt(0).toUpperCase() + selectedEmail.category.slice(1)}
                        </span>
                      </div>
                      <p className="text-sm text-muted-foreground">
                        From: {selectedEmail.sender} • {selectedEmail.date}
                      </p>
                    </CardHeader>
                    <CardContent>
                      <p className="whitespace-pre-line">{selectedEmail.preview}</p>
                    </CardContent>
                  </Card>
                </div>
              ) : (
                <div className="space-y-2">
                  {filteredEmails.length === 0 ? (
                    <p className="text-center py-8 text-muted-foreground">No emails in this category</p>
                  ) : (
                    filteredEmails.map(email => (
                      <Card 
                        key={email.id} 
                        className={`cursor-pointer hover:bg-gray-50 transition-colors ${!email.read ? 'border-l-4 border-l-primary' : ''}`}
                        onClick={() => setSelectedEmail(email)}
                      >
                        <CardContent className="p-4">
                          <div className="flex justify-between items-start">
                            <div>
                              <h4 className={`font-medium ${!email.read ? 'font-semibold' : ''}`}>{email.subject}</h4>
                              <p className="text-sm text-muted-foreground">{email.sender}</p>
                              <p className="text-sm mt-1 line-clamp-1">{email.preview}</p>
                            </div>
                            <div className="flex items-center gap-2">
                              <span className="text-xs text-muted-foreground">{email.date}</span>
                              <span className={`px-2 py-1 rounded-full text-xs font-medium ${categoryColors[email.category]}`}>
                                {email.category.charAt(0).toUpperCase() + email.category.slice(1)}
                              </span>
                            </div>
                          </div>
                        </CardContent>
                      </Card>
                    ))
                  )}
                </div>
              )}
            </div>
          </div>
        </CardContent>
      </Card>
    </div>
  )
}
