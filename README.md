# game2
high school i/o project
import java.util.Scanner;

public class DailyHealthAdventure {

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        HealthTracker player = new HealthTracker();

        System.out.println("====================================================");
        System.out.println("         A DAY IN YOUR LIFE: HEALTH QUEST           ");
        System.out.println("====================================================");
        System.out.println("Guide your character through their day! Every choice");
        System.out.println("affects your Hygiene, Nutrition, and Energy.\n");

        // SCENE 1: MORNING ROUTINE
        playScene(scanner, player,
            "7:30 AM - You just woke up! You're running a little behind schedule.",
            new Choice("Brush teeth, wash face, and eat a bowl of oatmeal", +20, +20, +15,
                "Great start! Your teeth are clean and slow-burning carbs fuel your morning."),
            new Choice("Skip brushing teeth and grab a sugary pastry on the way out", -15, -10, +10,
                "Quick, but sugar coats your unbrushed teeth and causes an energy crash later."),
            new Choice("Hit snooze, skip breakfast, and rush out the door", -5, -15, -10,
                "You got 15 extra minutes of sleep, but your stomach is growling already.")
        );

        // SCENE 2: BEFORE LUNCH
        playScene(scanner, player,
            "12:15 PM - Lunchtime! You just finished using shared computers and handling doorknobs.",
            new Choice("Wash hands with soap for 20 seconds, then eat a balanced lunch", +20, +20, +15,
                "Smart move! Washing away germs protects your immune system before eating."),
            new Choice("Use a quick splash of water and eat fast food fries and soda", 0, -15, -5,
                "Water alone misses germs, and heavy grease makes you sluggish."),
            new Choice("Start eating a healthy salad immediately without washing hands", -20, +15, +10,
                "Great food choice, but unwashed hands transfer germs straight to your meal!")
        );

        // SCENE 3: AFTERNOON SLUMP
        playScene(scanner, player,
            "3:30 PM - Afternoon break! You feel your focus starting to fade.",
            new Choice("Drink a tall glass of water and take a 15-minute brisk walk", +5, +10, +20,
                "Hydration and movement boost blood flow to your brain naturally."),
            new Choice("Drink an energy drink and scroll on your phone", 0, -15, +5,
                "Temporary caffeine boost, but high sugar and eye strain catch up to you."),
            new Choice("Eat an apple with peanut butter", +5, +20, +15,
                "Fiber and healthy fats stabilize your afternoon energy!")
        );

        // SCENE 4: BEDTIME ROUTINE
        playScene(scanner, player,
            "9:30 PM - Time for bed! You're tired from a long day.",
            new Choice("Brush & floss teeth, wash hands/face, and sleep 8 hours", +25, +5, +25,
                "Text-book bedtime routine! Your body and teeth recover overnight."),
            new Choice("Fall straight into bed in your daytime clothes without brushing", -25, 0, +10,
                "Bacteria build up overnight on your teeth and skin."),
            new Choice("Brush teeth, then stay up until 1:00 AM gaming", +15, 0, -25,
                "Clean teeth, but losing sleep weakens your immune system for tomorrow.")
        );

        // END OF DAY EVALUATION
        player.printFinalReport();
        scanner.close();
    }

    private static void playScene(Scanner scanner, HealthTracker player, String scenario,
                                  Choice c1, Choice c2, Choice c3) {
        Choice[] choices = {c1, c2, c3};

        System.out.println("\n----------------------------------------------------");
        System.out.println(scenario);
        System.out.println("----------------------------------------------------");
        for (int i = 0; i < choices.length; i++) {
            System.out.println((i + 1) + ". " + choices[i].text);
        }

        int pick = -1;
        while (pick < 1 || pick > 3) {
            System.out.print("Your decision (1-3): ");
            if (scanner.hasNextInt()) {
                pick = scanner.nextInt();
            } else {
                scanner.next();
            }
        }

        Choice chosen = choices[pick - 1];
        player.applyChoice(chosen);
        System.out.println("\n>> " + chosen.feedback);
        player.printCurrentStatus();
    }
}

class HealthTracker {
    private int hygiene = 50;
    private int nutrition = 50;
    private int energy = 50;

    public void applyChoice(Choice c) {
        hygiene = clamp(hygiene + c.hygieneDelta);
        nutrition = clamp(nutrition + c.nutritionDelta);
        energy = clamp(energy + c.energyDelta);
    }

    private int clamp(int value) {
        return Math.max(0, Math.min(100, value));
    }

    public void printCurrentStatus() {
        System.out.println("[Stats] Hygiene: " + hygiene + "/100 | Nutrition: " + nutrition + "/100 | Energy: " + energy + "/100");
    }

    public void printFinalReport() {
        int overallScore = (hygiene + nutrition + energy) / 3;

        System.out.println("\n====================================================");
        System.out.println("              END OF DAY HEALTH REPORT              ");
        System.out.println("====================================================");
        System.out.println("Hygiene Score:   " + hygiene + " / 100");
        System.out.println("Nutrition Score: " + nutrition + " / 100");
        System.out.println("Energy Score:    " + energy + " / 100");
        System.out.println("----------------------------------------------------");
        System.out.println("OVERALL HEALTH SCORE: " + overallScore + " / 100");

        if (overallScore >= 85) {
            System.out.println("RANK: S (Wellness Master) - Outstanding daily habits!");
        } else if (overallScore >= 70) {
            System.out.println("RANK: A (Healthy & Balanced) - Great job taking care of yourself!");
        } else if (overallScore >= 50) {
            System.out.println("RANK: B (Getting By) - Good effort, but watch out for skipped habits.");
        } else {
            System.out.println("RANK: C (Burnout Risk) - Your body needs better hygiene, food, and rest!");
        }
        System.out.println("====================================================");
    }
}

class Choice {
    String text;
    int hygieneDelta;
    int nutritionDelta;
    int energyDelta;
    String feedback;

    public Choice(String text, int hygieneDelta, int nutritionDelta, int energyDelta, String feedback) {
        this.text = text;
        this.hygieneDelta = hygieneDelta;
        this.nutritionDelta = nutritionDelta;
        this.energyDelta = energyDelta;
        this.feedback = feedback;
    }
}
